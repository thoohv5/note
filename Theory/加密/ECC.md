---
title: ECC
date: 2026-04-07
  - 理论
  - 加密
type: reference
status: complete
---

## ECC

## 概述

在密码学中，**ECC（Elliptic Curve Cryptography，椭圆曲线密码学）** 是一种基于椭圆曲线数学构造的 **非对称加密算法体系**，相比 RSA 在相同安全级别下使用更短的密钥，性能更优。

| 特性 | ECC | RSA |
| --- | --- | --- |
| 算法类型 | 非对称加密（公钥/私钥） | 非对称加密 |
| 安全强度 | 高（更小密钥等效更强安全） | 中（需更长密钥保证安全） |
| 速度 | 更快（签名和解密更快） | 较慢 |
| 应用场景 | 数字签名、密钥交换、加密 | 同上 |
| 国密版本 | ✅ SM2（ECC 国密标准） | ❌ 不属于国密 |

### Go语言实现

Go 标准库原生支持 ECC（基于 `crypto/elliptic` 和 `crypto/ecdsa`）。以下是一个完整例子：

- **使用标准 ECC 实现签名和验证**
    
    ```go
    package main
    
    import (
    	"crypto/ecdsa"
    	"crypto/elliptic"
    	"crypto/rand"
    	"crypto/sha256"
    	"fmt"
    	"math/big"
    )
    
    func main() {
    	// 1. 生成 ECC 密钥对（使用 P256 曲线）
    	privateKey, err := ecdsa.GenerateKey(elliptic.P256(), rand.Reader)
    	if err != nil {
    		panic(err)
    	}
    	publicKey := &privateKey.PublicKey
    
    	message := "ECC demo in Golang"
    	hash := sha256.Sum256([]byte(message))
    
    	// 2. 签名
    	r, s, err := ecdsa.Sign(rand.Reader, privateKey, hash[:])
    	if err != nil {
    		panic(err)
    	}
    	fmt.Printf("Signature: (r: %x, s: %x)\n", r, s)
    
    	// 3. 验签
    	valid := ecdsa.Verify(publicKey, hash[:], r, s)
    	fmt.Println("Signature Verified:", valid)
    }
    
    ```
    

---

### ECC vs SM2

| ECC (Go 标准库) | SM2 (国密标准) |
| --- | --- |
| 使用 `elliptic.P256()` | 使用特定国密曲线参数（非标准 P256） |
| 标准 ECDSA 签名算法 | 定制国密 SM2 签名/加密算法 |
| 国际通用 | 国内政务/金融合规使用 |

若你在中国国内做政务或金融项目，**推荐使用 SM2（国密 ECC 实现）**；否则标准 ECC 也广泛应用于区块链、JWT、TLS 等场景。

---