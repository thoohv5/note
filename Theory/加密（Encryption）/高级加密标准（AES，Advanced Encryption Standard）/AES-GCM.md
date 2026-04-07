# AES-GCM

# 概述

### **GMAC (Galois message authentication code mode, 伽罗华消息认证码)**

GMAC就是利用伽罗华域(Galois Field，GF，有限域)乘法运算来计算消息的MAC值

### **GCM(Galois/Counter Mode)**

GCM是认证加密模式中的一种，它结合了上述两者的特点(GCM中的G就是指GMAC，C就是指CTR)，能同时确保数据的保密性、完整性及真实性，另外，它还可以提供附加消息的完整性校验

就像CTR模式下一样，先对块进行顺序编号，然后将该块编号与初始向量(IV)组合，并使用密钥k，对输入做AES加密，然后，将加密的结果与明文进行XOR运算来生成密文。像CTR模式下一样，应该对每次加密使用不同的IV。对于附加消息，会使用密钥H(由密钥K得出)，运行GMAC，将结果与密文进行XOR运算，从而生成可用于验证数据完整性的身份验证标签。最后，密文接收者会收到一条完整的消息，包含密文、IV(计数器CTR的初始值)、身份验证标签(MAC值)。

GCM 模式提供了加密和认证功能，更安全且不需要填充。

- 示例
    
    ```go
    package main
    
    import (
    	"crypto/aes"
    	"crypto/cipher"
    	"crypto/rand"
    	"encoding/hex"
    	"fmt"
    	"io"
    )
    
    // AESEncryptGCM AES-GCM 加密
    func AESEncryptGCM(plaintext, key []byte) ([]byte, error) {
    	block, err := aes.NewCipher(key)
    	if err != nil {
    		return nil, err
    	}
    
    	gcm, err := cipher.NewGCM(block)
    	if err != nil {
    		return nil, err
    	}
    
    	nonce := make([]byte, gcm.NonceSize())
    	if _, err = io.ReadFull(rand.Reader, nonce); err != nil {
    		return nil, err
    	}
    
    	return gcm.Seal(nonce, nonce, plaintext, nil), nil
    }
    
    // AESDecryptGCM AES-GCM 解密
    func AESDecryptGCM(ciphertext, key []byte) ([]byte, error) {
    	block, err := aes.NewCipher(key)
    	if err != nil {
    		return nil, err
    	}
    
    	gcm, err := cipher.NewGCM(block)
    	if err != nil {
    		return nil, err
    	}
    
    	nonceSize := gcm.NonceSize()
    	if len(ciphertext) < nonceSize {
    		return nil, fmt.Errorf("ciphertext too short")
    	}
    
    	nonce, ciphertext := ciphertext[:nonceSize], ciphertext[nonceSize:]
    	return gcm.Open(nil, nonce, ciphertext, nil)
    }
    
    func main() {
    	key := []byte("examplekey123456") // 16字节密钥(AES-128)
    	plaintext := []byte("Hello, AES-GCM!")
    
    	// 加密
    	encrypted, err := AESEncryptGCM(plaintext, key)
    	if err != nil {
    		panic(err)
    	}
    	fmt.Printf("Encrypted: %x\n", encrypted)
    
    	// 解密
    	decrypted, err := AESDecryptGCM(encrypted, key)
    	if err != nil {
    		panic(err)
    	}
    	fmt.Printf("Decrypted: %s\n", decrypted)
    }
    ```