# Cross-chain-Settlement
AEON enables users to pay with assets on one chain (e.g., SOL) and settle transactions on another (e.g., purchasing BNB Chain memecoins), ensuring seamless, transparent, and secure settlement across chains.

## API Integration

### Introduction

The merchant calls up a checkout counter, and the user pays with cryptocurrency. After the user pays with cryptocurrency at the front end, we send order information via webhook.

Merchant can check more details at the merchant dashboard.

### Quick Start

Before starting integration, you need to obtain the customerNum and secret for technical integration. Please contact our business team to obtain the appid and secret for the production environment.

Before obtaining your production environment account, you can integrate with our test environment first. 

Please contact us for sandbox environment account.

### Environmental Information

Sandbox Environment:`https://sbx-crypto-payment-api.aeon.xyz`  
Production Environment: `https://crypto-payment-api.aeon.xyz`

### Merchant Configuration

These information should be given for configuration.

| Configuration items | Mandatory | Details |
|:-------------|:-------------|:-------------|
| Merchant Logo | Y        | Display at the top of the checkout        |
| Main Button Color         | N          | According to the standard below         |
| Token        | Y          |  Default option includes all supported tokens       |
| Gateway        | Y          |   Payment Channel      |
| Payment Slippage        | N          |   **With Configuration Method** :± X%  <br> Due to currency price fluctuations, when users use non-stable currencies for payment, if the user's payment is successful, the merchant will be paid at the current market price, and the settlement amount will have the fee deducted. If the loss exceeds the slippage set by the merchant, the payment will fail. If the AEON fee deducted is more than 1% of the order amount, it will not be settled to the merchant.  <br> For example: if a merchant sets a 1% Payment Slippage, and a user orders 100U worth of a non-stable coin, after payment, if this non-stable coin slips to 98U worth, the order will fail and to be refunded. However, if this non-stable coin increases to 102U worth, the order will settle with 101U being paid to the merchant.  <br> **Without Configuration**  <br> If not configured, the customer assumes the risk of currency price fluctuations by default. After AEON receives the user's payment, it deducts the AEON fee and settles the whole amount to the merchant.      |
| redirectURL        |  Y         |    User returns to the merchant's designated default page in any case     |
| Settlement Currency        | Y          |   General settlement token: **USDT** <br> Settlement fiat currency to be configured: **USD, EUR, HKD, VND, IDR, THB**      |
| Settlement Address        |   Y        |   Token address receiving settlement      |
| IP Whitelist        |    Y       |    For security reasons, the merchant's export IP needs to be whitelisted     |
| Domain Whitelist        |     Y      |    For security reasons, the merchant's Domain needs to be whitelisted     |
| Refund Order Callbackurl        |        N   |    Url for receiving refund order webhook     |
| Transaction Fee        |     Y      |   User Responsibility / Merchant Responsibility  <br> Example: Order amount $100, fee rate 3% <br> <br> Merchant Responsibility:  <br> The merchant absorbs the transaction fee cost. The user pays $100, and the merchant is charged a fee of $3. Therefore, the settlement amount for the merchant is $100 - $3 = $97. <br> <br> User Responsibility:  <br> The user covers the merchant's transaction fee. For an order amount of $100, the user pays $103, and the fee is $3. Thus, the settlement amount for the merchant remains $100.     |

![image](https://github.com/user-attachments/assets/d767635d-890b-41bb-b022-4d16ac282a2d)



### Supported payCurrency:

Please contact us for configuration.

| Currency         | Regular                                                                               | Example    |
| :--------------- | :------------------------------------------------------------------------------------ | :--------- |
| USD              | 2 non-zero decimal places after the decimal point                                     | 100.12     |
| EUR              | 2 non-zero decimal places after the decimal point                                     | 100.12     |
| HKD              | 2 non-zero decimal places after the decimal point                                     | 100.12     |
| IDR              | integer                                                                               | 1000       |
| THB              | integer                                                                               | 1000       |
| VND              | integer                                                                               | 1000       |
| Token Name(USDT) | The token used for unit configured, 8 non-zero decimal places after the decimal point | 1.12345678 |

### Refund Rules for Orders

The following four situations will trigger the automatic refund process on AEON page.

Users can initiate an automatic refund process on the interface by filling out refund details. 

Otherwise, the order amount will be settled to the merchant if the user uncompleted the refund on AEON side.

| Fail reason                                                                                                                                                                                                                                            | Fail type            |
| :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------- |
| Sending amount lower than order amount, order failed                                                                                                                                                                                                   | lower_amount         |
| When the merchant has set payment slippage, the difference in the floating exchange rate of the payment token is greater than the payment slippage, causing the payment amount to be less than the order amount, an automatic refund will be triggered | currency_fluctuation |
| User send crypto with different crypto or network                                                                                                                                                                                                      | wrong_payment        |
| Payment success and amount over order amount more than ≥5U                                                                                                                                                                                             | /                    |

## About the Sign

### Introduction

**The secret key used to generate a signature for API request input parameters should be securely maintained by the merchant.**

To generate a signature string:

1. Collect all required parameters that need to be verified into an array.
2. Sort the array alphabetically by parameter names (keys) in ascending ASCII order. It's important to note that only the parameter keys are sorted, not their values.
3. Exclude the sign parameter itself from the array before generating the signature.

### Example

#### Example request parameters

```json JSON
{
    "appId": "TEST000001",
    "sign": "TEST000001",
    "merchantOrderNo": "11126",
    "userId": "123456789@qq.com",
    "orderAmount": "1000",
    "payCurrency": "USD",
    "paymentTokens": "USDT,ETH",
    "paymentExchange": "16f021b0-f220-4bbb-aa3b-82d423301957,9226e5c2-ebc3-4fdd-94f6-ed52cdce1420"
}
```

Prepare the string concatenation for sign, convert the format to parameter_name=parameter_value, link with '&', and finally append the key (secret). The result is as follows:

```Text JSON
appId=TEST000001&merchantOrderNo=11126&orderAmount=1000&payCurrency=USD&paymentExchange=16f021b0-f220-4bbb-aa3b-82d423301957,9226e5c2-ebc3-4fdd-94f6-ed52cdce1420&paymentTokens=USDT,ETH&userId=505884978@qq.com&key=**************0b5nCak
```

Continuing from the prepared string for signing, perform the SHA-512 algorithm to generate the final signature.And change it into capital letters. The signature result is as follows:

```Text JSON
3962E8FF2ABD24B806D744C6630B95A05855A2AB86944CCF52009D6E2582787EB0F34CFF323843DDA55B148D770390598AF335DDBECC61D702AA1A87EE93D
```

#### Java signature generation code example

```java Java
import java.io.*;
import java.net.*;
import java.security.*;
import java.util.*;

public class Sha512Signer {
    
    public static String sha512Sign(Map<String, String> dataMap, String secretKey) throws NoSuchAlgorithmException {

        List<String> keys = new ArrayList<>(dataMap.keySet());
        Collections.sort(keys);


        StringBuilder queryString = new StringBuilder();
        for (String key : keys) {
            queryString.append(key).append("=").append(dataMap.get(key)).append("&");
        }


        queryString.append("key=").append(secretKey);
        String stringToSign = queryString.toString();
        System.out.println("String to sign: " + stringToSign);

        MessageDigest md = MessageDigest.getInstance("SHA-512");
        byte[] hash = md.digest(stringToSign.getBytes());
        StringBuilder hexString = new StringBuilder();
        for (byte b : hash) {
            hexString.append(String.format("%02x", b));
        }

        return hexString.toString().toUpperCase();
    }

    public static void main(String[] args) throws Exception {

        String url = "https://crypto-payment.alchemytech.cc/XXXXX";
        String appId = "XXXXX";
        String secret = "XXXXX";


        Map<String, String> reqData = new HashMap<>();
        reqData.put("merchantOrderNo", "XXXXX");
        reqData.put("appId", appId);


        String sign = sha512Sign(reqData, secret);
        reqData.put("sign", sign);


        StringBuilder jsonData = new StringBuilder("{");
        for (Map.Entry<String, String> entry : reqData.entrySet()) {
            jsonData.append("\"").append(entry.getKey()).append("\":\"").append(entry.getValue()).append("\",");
        }
        jsonData.deleteCharAt(jsonData.length() - 1); 
        jsonData.append("}");


        sendPostRequest(url, jsonData.toString());
    }


    private static void sendPostRequest(String url, String jsonData) throws IOException {
        URL obj = new URL(url);
        HttpURLConnection con = (HttpURLConnection) obj.openConnection();
        

        con.setRequestMethod("POST");
        con.setRequestProperty("Content-Type", "application/json");
        con.setDoOutput(true);
        

        try (OutputStream os = con.getOutputStream()) {
            byte[] input = jsonData.getBytes("utf-8");
            os.write(input, 0, input.length);	
        }


        int responseCode = con.getResponseCode();
        System.out.println("Response Code: " + responseCode);
        
        try (BufferedReader in = new BufferedReader(new InputStreamReader(con.getInputStream()))) {
            String inputLine;
            StringBuilder response = new StringBuilder();
            while ((inputLine = in.readLine()) != null) {
                response.append(inputLine);
            }

            System.out.println("Response: " + response.toString());
        }
    }
}
```
```python Python
import requests
import hashlib


def sha512_sign(data_dict, secret_key):
    sorted_items = sorted(data_dict.items())
    query_string = '&'.join(f"{str(k)}={str(v)}" for k, v in sorted_items)
    string_to_sign = query_string + f"&key={secret_key}"
    print(string_to_sign)
    sha512_hash = hashlib.sha512(string_to_sign.encode('utf-8')).hexdigest().upper()
    return sha512_hash

urls = "https://crypto-payment.alchemytech.cc/XXXXX"
appId = "XXXXX"
secret = "XXXXX"
req_data = {
    "merchantOrderNo":"XXXXX",
    "appId":appId,
}

sign = sha512_sign(req_data, secret)
req_data["sign"] = sign

print(requests.post(urls, json=req_data).text)
```
```go
package main

import (
	"bytes"
	"crypto/sha512"
	"encoding/hex"
	"fmt"
	"io/ioutil"
	"net/http"
	"sort"
	"strings"
)

func sha512Sign(dataMap map[string]string, secretKey string) string {
	keys := make([]string, 0, len(dataMap))
	for key := range dataMap {
		keys = append(keys, key)
	}
	sort.Strings(keys)

	var queryStrings []string
	for _, key := range keys {
		queryStrings = append(queryStrings, fmt.Sprintf("%s=%s", key, dataMap[key]))
	}
	queryString := strings.Join(queryStrings, "&")


	stringToSign := queryString + "&key=" + secretKey
	fmt.Println("String to sign:", stringToSign)


	hash := sha512.New()
	hash.Write([]byte(stringToSign))
	signature := hex.EncodeToString(hash.Sum(nil))
	return strings.ToUpper(signature)
}

func main() {
	url := "https://crypto-payment.alchemytech.cc/XXXXX"
	appId := "XXXXX"
	secret := "XXXXX"


	reqData := map[string]string{
		"merchantOrderNo": "XXXXX",
		"appId":           appId,
	}


	sign := sha512Sign(reqData, secret)
	reqData["sign"] = sign

	jsonData := "{"
	for key, value := range reqData {
		jsonData += fmt.Sprintf("\"%s\":\"%s\",", key, value)
	}
	jsonData = jsonData[:len(jsonData)-1] + "}"

	resp, err := http.Post(url, "application/json", bytes.NewBuffer([]byte(jsonData)))
	if err != nil {
		fmt.Println("Error sending request:", err)
		return
	}
	defer resp.Body.Close()


	body, err := ioutil.ReadAll(resp.Body)
	if err != nil {
		fmt.Println("Error reading response:", err)
		return
	}

	fmt.Println("Response:", string(body))
}
```

Create Order
