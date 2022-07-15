- [V2X消息签名和验签流程——应用层安全_哈哈不是嘿嘿的博客-CSDN博客_v2x证书](https://blog.csdn.net/qq_34432784/article/details/106579609)

## **1.简介**

为了保证V2X消息的来源可靠性(authenticity)和完整性(integrity)，发送方在发送V2X消息时需要对消息进行签名，当然用于对消息签名的证书是合法的，这也是前提，具体涉及到PKI(Public key infrastructure)系统，这里不做详谈。接收方接收到V2X消息后首先需要对证书进行验证，进一步利用证书上的[公钥](https://so.csdn.net/so/search?q=公钥&spm=1001.2101.3001.7020)对V2X消息的签名进行验证，以保证V2X消息的可靠性和完整性。

## **2.V2X消息签名流程**

发送方在发送V2X消息时需要对消息进行签名，签名流程如下图。在 ITS_SignData 接口函数中，V2X消息（Tbs Data）、签名者信息和签名算法（SM2, ECDSA等）传递给数据处理实体，首先对签名者消息进行编码，然后对编码值进行哈希计算，得到签名者信息的摘要，然后需要把V2X信息和headerInfo进行整合，headerInfo 主要包括PSID, 消息生成时间、地点等，具体的细节可在上一篇[中国C-V2X SPDU格式解读](https://blog.csdn.net/qq_34432784/article/details/106159429)文章查看。对整合后的消息tbs data进行编码、摘要计算，对tbs data的摘要和证书的摘要进行整合，利用签名者私钥（secure storage 安全储存）对整合值进行签名计算，得到签名值。对tbs data、签名者信息和签名进行整合、得到Secured Message, 经过编码以及序列化后通过OBU/RSU发送。

![V2X消息签名流程图](https://img-blog.csdnimg.cn/20200605223923828.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0NDMyNzg0,size_16,color_FFFFFF,t_70#pic_center)

## **3.V2X消息验签流程**

OBU/RSU 接收到V2X消息后需要对消息进行签名验证，签名验证流程如下图。在 ITS_VerifySignedData 接口函数中,首先对接收到的Secured Message 进行解码，获取签名者信息、tbs data以及签名，然后验证证书是否有效，消息是否为自签、证书是否包含签名者公钥以及V2X消息是否有效，任何一项fail，都会丢弃此消息。如果此前项都验证通过，下一步开始对签名者消息进行编码摘要计算（图中摘要未写出），得到签名者摘要，进一步进行tbs data摘要值计算，对两个摘要值进行整合，利用证书上的公钥和摘要值对签名进行验签，返回签名验证结果。

![V2X消息验签流程图](https://img-blog.csdnimg.cn/20200605231617149.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0NDMyNzg0,size_16,color_FFFFFF,t_70#pic_center)

## **4.总结**

本文对V2X[应用层](https://so.csdn.net/so/search?q=应用层&spm=1001.2101.3001.7020)安全进行了简单的解读，主要参考了GB/T 37374-2019 智能交通 数字证书应用接口规范文档。