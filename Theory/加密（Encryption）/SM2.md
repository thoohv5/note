---
title: SM2
date: 2026-04-07
tags: [理论, 加密]
type: note
status: complete
---

# SM2

### 椭圆曲线公钥密码算法（用于 **非对称加密**）

- 类似于：RSA / ECC
- 用途：数字签名、密钥交换、公钥加密
- 特点：
    - 基于椭圆曲线密码学（ECC）
    - 安全性与 ECC 接近，但更适用于中国标准和政务、金融等领域
- 应用场景：
    - 数字证书（如国密版的 HTTPS）
    - 身份认证、签名验证等

- SM2 加密/解密 和 签名/验签
    
    ```go
    package main
    
    import (
    	"fmt"
    	"github.com/tjfoc/gmsm/sm2"
    )
    
    func main() {
    	// 生成 SM2 密钥对
    	priv, err := sm2.GenerateKey()
    	if err != nil {
    		panic(err)
    	}
    	pub := &priv.PublicKey
    
    	data := []byte("Hello SM2")
    
    	// 加密
    	ciphertext, err := pub.Encrypt(data)
    	if err != nil {
    		panic(err)
    	}
    	fmt.Printf("SM2 Encrypted: %x\n", ciphertext)
    
    	// 解密
    	plaintext, err := priv.Decrypt(ciphertext)
    	if err != nil {
    		panic(err)
    	}
    	fmt.Printf("SM2 Decrypted: %s\n", plaintext)
    
    	// 签名
    	sign, err := priv.Sign(nil, data, nil)
    	if err != nil {
    		panic(err)
    	}
    	fmt.Printf("SM2 Signature: %x\n", sign)
    
    	// 验签
    	ok := pub.Verify(data, sign)
    	fmt.Println("SM2 Signature Verified:", ok)
    }
    
    ```