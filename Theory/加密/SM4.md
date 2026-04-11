---
title: SM4
date: 2026-04-07
tags: [理论, 加密]
type: note
status: complete
---

# SM4

### SM4：分组对称加密算法

- 类似于：AES（高级加密标准）
- 用途：数据加密与解密
- 特点：
    - 块大小：128 位
    - 密钥长度：128 位
    - 加密模式：支持 ECB、CBC、CFB 等常见模式
- 应用场景：
    - 文件/数据传输加密
    - TLS/SSL 中对称加密部分（在国密协议中）

- SM4 CBC 模式安全封装（带加密、解密、填充处理）
    
    ```go
    package main
    
    import (
    	"bytes"
    	"crypto/cipher"
    	"crypto/rand"
    	"fmt"
    	"github.com/tjfoc/gmsm/sm4"
    	"io"
    )
    
    // PKCS#7 padding
    func pkcs7Padding(data []byte, blockSize int) []byte {
    	padding := blockSize - len(data)%blockSize
    	padtext := bytes.Repeat([]byte{byte(padding)}, padding)
    	return append(data, padtext...)
    }
    
    // PKCS#7 unpadding
    func pkcs7UnPadding(data []byte) ([]byte, error) {
    	length := len(data)
    	if length == 0 {
    		return nil, fmt.Errorf("invalid padding size")
    	}
    	unpadding := int(data[length-1])
    	if unpadding > length || unpadding == 0 {
    		return nil, fmt.Errorf("invalid padding")
    	}
    	return data[:(length - unpadding)], nil
    }
    
    // 加密（CBC 模式）
    func sm4EncryptCBC(key, plainText []byte) ([]byte, []byte, error) {
    	block, err := sm4.NewCipher(key)
    	if err != nil {
    		return nil, nil, err
    	}
    
    	blockSize := block.BlockSize()
    	plainText = pkcs7Padding(plainText, blockSize)
    
    	// 随机生成 IV
    	iv := make([]byte, blockSize)
    	if _, err := io.ReadFull(rand.Reader, iv); err != nil {
    		return nil, nil, err
    	}
    
    	mode := cipher.NewCBCEncrypter(block, iv)
    	cipherText := make([]byte, len(plainText))
    	mode.CryptBlocks(cipherText, plainText)
    
    	return cipherText, iv, nil
    }
    
    // 解密（CBC 模式）
    func sm4DecryptCBC(key, cipherText, iv []byte) ([]byte, error) {
    	block, err := sm4.NewCipher(key)
    	if err != nil {
    		return nil, err
    	}
    
    	if len(cipherText)%block.BlockSize() != 0 {
    		return nil, fmt.Errorf("invalid ciphertext length")
    	}
    
    	mode := cipher.NewCBCDecrypter(block, iv)
    	plainText := make([]byte, len(cipherText))
    	mode.CryptBlocks(plainText, cipherText)
    
    	return pkcs7UnPadding(plainText)
    }
    
    // 示例主函数
    func main() {
    	key := []byte("1234567890abcdef") // 16 字节密钥
    	data := []byte("国密 SM4 CBC 模式加密测试")
    
    	// 加密
    	cipherText, iv, err := sm4EncryptCBC(key, data)
    	if err != nil {
    		panic(err)
    	}
    	fmt.Printf("CipherText: %x\n", cipherText)
    	fmt.Printf("IV        : %x\n", iv)
    
    	// 解密
    	plainText, err := sm4DecryptCBC(key, cipherText, iv)
    	if err != nil {
    		panic(err)
    	}
    	fmt.Printf("Decrypted : %s\n", plainText)
    }
    
    ```