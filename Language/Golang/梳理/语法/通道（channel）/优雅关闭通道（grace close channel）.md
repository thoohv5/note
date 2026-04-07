# 优雅关闭通道（grace close channel）

### 一个 sender，一个 receiver

```go
	msg := make(chan string)

	go func() {
		fmt.Println(<-msg)
	}()

	msg <- "abcd"
	close(msg)
```

### 一个 sender， M 个 receiver

```go
rand.Seed(time.Now().UnixNano())
log.SetFlags(0)

// ...
const MaxRandomNumber = 100000
const NumReceivers = 100
wgReceivers := sync.WaitGroup{}
wgReceivers.Add(NumReceivers)

// ...
dataCh := make(chan int)

// the sender
go func() {
	for {
		if value := rand.Intn(MaxRandomNumber); value == 0 {
			// the only sender can close the channel safely.
			close(dataCh)
			return
		} else {
			dataCh <- value
		}
	}
}()

// receivers
for i := 0; i < NumReceivers; i++ {
	go func() {
		defer wgReceivers.Done()

		// receive values until dataCh is closed and
		// the value buffer queue of dataCh is empty.
		for value := range dataCh {
			log.Println(value)
		}
	}()
}

wgReceivers.Wait()
```

### N 个 sender，一个 reciver

解决方案就是增加一个传递关闭信号的 channel，receiver 通过信号 channel 下达关闭数据 channel 指令。senders 监听到关闭信号后，停止发送数据。

在 Go 语言中，对于一个 channel，如果最终没有任何 goroutine 引用它，不管 channel 有没有被关闭，最终都会被 gc 回收。

```go
rand.Seed(time.Now().UnixNano())
log.SetFlags(0)

// ...
const MaxRandomNumber = 100000
const NumSenders = 1000

wgReceivers := sync.WaitGroup{}
wgReceivers.Add(1)

// ...
dataCh := make(chan int)
stopCh := make(chan struct{})
// stopCh is an additional signal channel.
// Its sender is the receiver of channel dataCh.
// Its reveivers are the senders of channel dataCh.

// senders
for i := 0; i < NumSenders; i++ {
	go func() {
		for {
			value := rand.Intn(MaxRandomNumber)

			select {
			case <-stopCh:
				return
			case dataCh <- value:
			}
		}
	}()
}

// the receiver
go func() {
	defer wgReceivers.Done()

	for value := range dataCh {
		if value == MaxRandomNumber-1 {
			// the receiver of the dataCh channel is
			// also the sender of the stopCh cahnnel.
			// It is safe to close the stop channel here.
			close(stopCh)
			return
		}

		log.Println(value)
	}
}()

// ...
wgReceivers.Wait()
```

### N 个 sender， M 个 receiver

需要增加一个中间人，M 个 receiver 都向它发送关闭 dataCh 的“请求”，中间人收到第一个请求后，就会直接下达关闭 dataCh 的指令（通过关闭 stopCh，这时就不会发生重复关闭的情况，因为 stopCh 的发送方只有中间人一个）。另外，这里的 N 个 sender 也可以向中间人发送关闭 dataCh 的请求。

```go
rand.Seed(time.Now().UnixNano())
log.SetFlags(0)

// ...
const MaxRandomNumber = 100000
const NumReceivers = 10
const NumSenders = 1000

wgReceivers := sync.WaitGroup{}
wgReceivers.Add(NumReceivers)

// ...
dataCh := make(chan int)
stopCh := make(chan struct{})
// stopCh is an additional signal channel.
// Its sender is the moderator goroutine shown below.
// Its reveivers are all senders and receivers of dataCh.
toStop := make(chan string)
// the channel toStop is used to notify the moderator
// to close the additional signal channel (stopCh).
// Its senders are any senders and receivers of dataCh.
// Its reveiver is the moderator goroutine shown below.

var stoppedBy string

// moderator
go func() {
	stoppedBy = <-toStop // part of the trick used to notify the moderator
	// to close the additional signal channel.
	close(stopCh)
}()

// senders
for i := 0; i < NumSenders; i++ {
	go func(id string) {
		for {
			value := rand.Intn(MaxRandomNumber)
			if value == 0 {
				// here, a trick is used to notify the moderator
				// to close the additional signal channel.
				select {
				case toStop <- "sender#" + id:
				default:
				}
				return
			}

			// the first select here is to try to exit the
			// goroutine as early as possible.
			select {
			case <-stopCh:
				return
			default:
			}

			select {
			case <-stopCh:
				return
			case dataCh <- value:
			}
		}
	}(strconv.Itoa(i))
}

// receivers
for i := 0; i < NumReceivers; i++ {
	go func(id string) {
		defer wgReceivers.Done()

		for {
			// same as senders, the first select here is to
			// try to exit the goroutine as early as possible.
			select {
			case <-stopCh:
				return
			default:
			}

			select {
			case <-stopCh:
				return
			case value := <-dataCh:
				if value == MaxRandomNumber-1 {
					// the same trick is used to notify the moderator
					// to close the additional signal channel.
					select {
					case toStop <- "receiver#" + id:
					default:
					}
					return
				}

				log.Println(value)
			}
		}
	}(strconv.Itoa(i))
}

// ...
wgReceivers.Wait()
log.Println("stopped by", stoppedBy)
```

# 附录

[go语言学习--channel的关闭-CSDN博客](https://blog.csdn.net/weixin_34391854/article/details/94574753)