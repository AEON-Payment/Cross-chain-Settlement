# Cross-chain-Settlement
AEON enables users to pay with assets on one chain (e.g., SOL) and settle transactions on another (e.g., purchasing BNB Chain memecoins), ensuring seamless, transparent, and secure settlement across chains.

# Introduction

The merchant calls up a checkout counter, and the user pays with cryptocurrency. After the user pays with cryptocurrency at the front end, we send order information via webhook.

Merchant can check more details at the merchant dashboard.

# Quick Start

Before starting integration, you need to obtain the customerNum and secret for technical integration. Please contact our business team to obtain the appid and secret for the production environment.

Before obtaining your production environment account, you can integrate with our test environment first. 

Please contact us for sandbox environment account.

# Environmental Information

Sandbox Environment:`https://sbx-crypto-payment-api.aeon.xyz`  
Production Environment: `https://crypto-payment-api.aeon.xyz`

# Merchant Configuration

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



# Supported payCurrency:

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

# Refund Rules for Orders

The following four situations will trigger the automatic refund process on AEON page.

Users can initiate an automatic refund process on the interface by filling out refund details. 

Otherwise, the order amount will be settled to the merchant if the user uncompleted the refund on AEON side.

| Fail reason                                                                                                                                                                                                                                            | Fail type            |
| :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------- |
| Sending amount lower than order amount, order failed                                                                                                                                                                                                   | lower_amount         |
| When the merchant has set payment slippage, the difference in the floating exchange rate of the payment token is greater than the payment slippage, causing the payment amount to be less than the order amount, an automatic refund will be triggered | currency_fluctuation |
| User send crypto with different crypto or network                                                                                                                                                                                                      | wrong_payment        |
| Payment success and amount over order amount more than ≥5U                                                                                                                                                                                             | /                    |
