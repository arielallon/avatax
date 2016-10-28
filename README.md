# AvaTax Calc Connector

Agile, successful companies focus on their core business and outsource complex, administrative functions – such as payroll and sales tax management – to the experts. Avalara can address your sales tax challenges with AvaTax. It’s the fastest, easiest, most accurate and affordable way to calculate sales tax; manage exemption certificates; file returns; and remit payments across multiple tax regions. Our tax decision engine determines rates based on 100,000+ taxability rules in 11,000+ taxing jurisdictions and instantly applies them to each transaction within your Magento shopping cart. 

# One Pica AvaTax Connector – A Magento Extension

_Instructions and documentation for the One Pica AvaTax Connector_

## Table of Contents

1.  [Overview](#overview)
    1.  [Support](#support)
    2.  [Prerequisites](#prerequisites)
    3.  [Versions](#versions)
2.  [Getting Started Guide](#getting-started-guide)
    1.  [Installation](#installation)
    2.  [Configuration](#configuration)
    3.  [Product Setup](#product-setup)
    4.  [Customer Setup](#customer-setup)
3.  [Technical Details](#technical-details)
4.  [Important Notes](#important-notes)
5.  [Common Problems](#common-problems)

## Overview

In Magento, tax calculation typically occurs during checkout but may also happen at other times as well (e.g., shopping cart). When the order is placed, it is not immediately recorded in AvaTax. Since Magento supports multiple invoicing and multiple credit memos for the same order, orders are not recorded as a whole in AvaTax. Tax amounts are calculated for the order when the customer places the order, but nothing is recorded in Avalara until a new invoice or credit memo is created.

A cron task is run every hour to send pending invoices and credit memos to AvaTax. The status of each pending item can be found in the AvaTax Order Sync Queue in Sales > Tax > AvaTax Order Sync Queue. The Magento cron task _must_ be running at least every 15 minutes for the extension — and Magento — to work properly.

### Support

For support with your AvaTax account, please visit [avalara.com/technical-support](http://www.avalara.com/Technical-Support). This software will not work unless you have a valid AvaTax account. To obtain the required account information, please contact your Avalara representative.

### Prerequisites

*   Active AvaTax account with a company set up including applicable nexuses
*   Webserver running a Magento installation with required cron job
    *   AvaTax SDK requirements: PHP extensions SSL (OpenSSL) and SoapClient
    *   [Magento system requirements](http://www.magentocommerce.com/system-requirements)
    *   [Magento installation guide](http://www.magentocommerce.com/wiki/magento_installation_guide)
    *   [Magento cron job](http://www.magentocommerce.com/wiki/how_to_setup_a_cron_job)

### Versions

The latest version is **2.6.2** (October 22, 2015). It can be obtained via [Magento Connect](http://www.magentocommerce.com/magento-connect/sales-tax-extension-for-avalara-s-avatax.html), [Github](https://github.com/onepica/avatax) or [downloaded directly](releases/OnePica_AvaTax-2.6.2.tgz) from our website.

_The extension supports all current versions of Magento Community after 1.2 and current versions of Magento Enterprise after 1.5\. There are also no known issues for all versions of Magento Professional._

## Getting Started Guide

### Step 1: Installation

In addition to the instructions below, you can watch this [installation walkthrough](http://www.youtube.com/v/_z05FwPTgEw?fs=1&hl=en_US&rel=0&hd=1 "One Pica AvaTax Connector Installation Walkthrough :: :: width: 854, height: 510") video that show the complete installation on a Magento Community 1.4.1.0 website.

#### Typical Installation Process

1.  If applicable, disable caching (System > Cache Management) and disable compilation (System > Tools > Compilation)
2.  Download the extension via Magento Connect
    1.  Browse to System > Magento Connect > Magento Connect Manager and Log in
    2.  Change to the Settings tab and set Preferred State to "Beta," then click Save Settings
    3.  Change to the Extensions tab, paste in the Extension Key (`magento-community/OnePica_AvaTax` or `http://connect20.magentocommerce.com/community/OnePica_AvaTax` depending on your Magento version), click Install, and confirm that you are shown a success message.
    4.  Click Refresh to see the package successfully installed
    5.  Click Return to Admin in the upper right
3.  Flush permissions: Log out of the admin and log back in
4.  Enable the cache, turn compilation back on, and recompile if you were previously using those features

#### Installation Process for Legacy Versions of Magento Professional & Magento Enterprise

1.  If applicable, disable caching (System > Cache Management) and disable compilation (System > Tools > Compilation)
2.  Download the latest version (see [versions](http://www.onepica.com/magento-extensions/avatax/#versions) above)
3.  Extract the contents: Confirm the creation of:
    1.  /app/etc/modules/OnePica_AvaTax.xml
    2.  /app/code/community/OnePica/AvaTax/
4.  Enable the cache, turn compilation back on, and recompile if you were previously using those features

### Step 2: Configuration

Within the Magento Admin, browse to System > Configuration > Sales/Tax. Click on AvaTax, the first item in the accordion menu. [Screenshot](assets/screenshots/config-avatax.png) | [Video](http://www.youtube.com/v/2SAngKG1uoo?fs=1&hl=en_US&rel=0&hd=1 "One Pica AvaTax Connector Installation Walkthrough :: :: width: 854, height: 510").

#### Connection Settings

*   _Action_: By default, the extension is installed in a disabled state. Once you get the configuration settings completely filled in, activate the extension. The Action should be set to "Enabled: calculate tax, submit data, and commit" unless you've been told specifically to set it to something else as choosing to not submit data or commit will remove the option of using AvaTax's reporting functionalities.
*   _URL_: Most people will only need the production URL which is `https://avatax.avalara.net`. If you have a development account, you can use `https://development.avalara.net` for testing.
*   _Account Number_: Your AvaTax account number, which you get from your AvaTax representative
*   _License Key_: Your Avatax license key, which you get from your AvaTax representative
*   _Company Code_: This can be found in your AvaTax Dashboard. This may be different from the actual company name. Your AvaTax representative can help with this if necessary
*   _Filter Requests by Region_: If you never collect tax in some regions, you can choose not to send data to AvaTax at all for some transactions.
    *   _None_: All transactions are sent to AvaTax, regardless of the taxability of the order
    *   _Filter tax calculations_: Tax calculation and reporting only occur for the regions selected — address validation and normalization still occur for all transactions (if enabled in settings below)
    *   _Filter tax calculations & address options_: All types of AvaTax services only occur for the regions selected

#### Log & Queue Settings

*   _Log Mode_: There are three levels of logging. It is recommended to use "Log all actions" in case there is an issue. This will record all the communication between Magento and AvaTax. For customers with many transactions, "Log only errors" can be used to reduce the size of the number of records in the database.
*   _Log Entry Lifetime_: Records in the log are automatically deleted after time.
*   _Successful Queue Lifetime_: Successful queue entries are no longer needed by the system and are automatically deleted after time.
*   _Failed Queue Lifetime_: Failed queue entries can no longer be sent to AvaTax and are automatically deleted after time.
*   _Log Type_: Select what is logged

#### Data Mapping

*   _Shipping SKU, Adjustment Refund SKU, and Adjustment Fee SKU_: SKUs sent to AvaTax for the associated event. For example, when tax is requested for a single-product order sent to state X, it's possible state X charges tax on shipping so two "products" will be sent in the request; one for the cart item and another for shipping. The correct shipping tax class will always be sent; however, this allows you to customize the SKU in case you want to add custom functionality in your AvaTax dashboard. The same is true when creating an adjustment refund or fee in the Magento Admin.
*   The remaining fields are optional and will be passed to AvaTax when committing sales. These are used only in AvaTax and are kept largely for reporting and filtering purposes.

#### Shipping Address Options

*   _Address Validation_: This optional step pre-checks the address against AvaTax's address services.
    *   _Enabled + Prevent Order_ The customer must enter a valid address during checkout to continue.
    *   _Enabled + Allow Order_ the customer must enter a valid address during checkout but will not prevent them from proceeding.
    *   _Disabled, a very basic level of validation is still preformed to verify that AvaTax can calculate tax on the given address. Typically, only a valid ZIP code is required._

_*   _Address Normalization_: This optional step pre-checks the address against AvaTax's address services. If enabled, the customer's address will be replaced with a well-formed address provided by AvaTax during checkout. A message to the customer will also be displayed if this occurs.*   _Frontend Error Message, Backend Error Message_: Messages shown to the user during checkout if an error occurs.*   _Notification Toolbar_: This will display on all admin pages if there are any errors with items in the queue (like admin messages). This is highly recommended._

_

#### Additional Magento Configurations

*   Within the Magento Admin, browse to System > Configuration > Sales/Shippings. Fill in all fields in the Origin accordion. This address is sent to AvaTax as the origin address. [Screenshot](assets/screenshots/config-origin-address.png)

### Step 3: Product Setup (optional)

*   Within the Magento Admin, browse to Sales > Tax > Product Tax Classes in the main navigation. When Magento attempts to quote tax with the AvaTax extension enabled, included in the information is the product tax category from Magento. Click on each Product Tax Class in the list and specify the AvaTax-defined tax class (PC040100, for example, is Clothing – General). Obtain these codes from your AvaTax representative or the documentation they provide to determine which codes are appropriate for your store. If left blank, AvaTax will use a General Sales and Use rate. Within AvaTax, product tax classes are called "product tax code." [Screenshot](assets/screenshots/config-product-tax-classes.png) | [Video](http://www.youtube.com/v/yfgeTOSxnXk?fs=1&hl=en_US&rel=0&hd=1 "One Pica AvaTax Connector Installation Walkthrough :: :: width: 854, height: 510")
*   Optionally, you can manage your product individually by SKU with the AvaTax Dashboard. Contact your AvaTax support representative for more information about how to set this up.

### Step 4: Customer Setup (optional)

_Many store owners will not need to use this feature_. Unless you have customers with special tax excemptions, you most likely do not need to set this up. If you are unsure, contact your AvaTax support representative for more information about how to use customer usage types.

*   Within the Magento Admin, browse to Sales > Tax > Customer Tax Classes in the main navigation. As with the Product Tax Classes, these are passed to AvaTax as part of the quoting process. Repeat the same process as with Product Tax Classes in this category. This is likely to be blank but contact your representative with any questions about the customer tax classes in the AvaTax Dashboard. Within AvaTax, customer tax classes are called customer "usage types." [Screenshot](assets/screenshots/config-customer-tax-classes.png)

## Technical Details

There are three major functionalities with the AvaTax extension: address validation/normalization, tax rate lookups, and sending invoice/credit memo info to AvaTax.

### Address validation/normalization

During checkout, shipping addresses are always sent to AvaTax for validation. The response returns a Boolean value to say if the address is valid, a Boolean value to say if enough of the address is valid to calculate tax (even if not all of it is), a normalized form of the address, and any possible error messages.

If address validation is enabled in the admin, the customer must provide a valid address before continuing. If address validation is turned off, the address validation call is still made to ensure the address is valid enough for AvaTax to calculate tax later. Typically, this just means the state and zip code match. There is no way to disable this basic validation check.

If address normalization is enabled in the admin, the normalized address that is returned from the validation check replaces the address provided by the customer.

_See: OnePica_AvaTax_Model_Avatax_Address_

### Tax rate lookup

When a sales quote wants to calculate rate, the quote items are sent to AvaTax to get tax rates. These rates are cache in the customer’s sessions, but if anything in the quote changes, the rates are refreshed. Nothing in AvaTax is recorded at this point.

_See: OnePica_AvaTax_Model_Avatax_Estimate_

### Sending order info to AvaTax

Whenever an invoice or credit memo is created, the data is added to the AvaTax Sync Queue. Every hour, on the hour, all pending data is passed to the AvaTax system to record purchases and returns. This is the only time order data is saved to AvaTax and will appear in the store owner’s dashboard.

_See: OnePica_AvaTax_Model_Avatax_Invoice_

## Important Notes

*   If an Admin is creating an order on the backend and the AvaTax config option Address Normalization is set to Enabled, then the address will be set on the new order during the create process. A notice will be posted to alert the Admin of this, but the order must be manually edited if the normalized address was incorrect.
*   Magento discounts are calculated into the price and not shown to Avalara as a discount. Currently, AvaTax does not support itemized discounting the way Magento does.
*   The extension has only been tested in USD and only works for addresses in the US and CA.
*   Transaction IDs in AvaTax are mapped to Magento's invoice and credit memo numbers; order number can be found in AvaTax's reference code 1 field on each record.
*   All credit memos will use the date of the order's first invoice to calculate the amount of tax to refund.
*   Default tax reporting in Magento displays tax collected by each tax rule you created in the system. However, when using AvaTax, Magento tax rules are not used; instead, tax rules and calculations are configured within AvaTax. Because of this, Magento's tax report will (correctly) show that there are no Magento tax rules collecting tax. A full tax report suite is available within your AvaTax dashboard.
*   Since tax rates and configurations can change, the tax amount of orders can also change from the time the customer checks out and when orders are invoiced. If this happens, items in the queue will change to a status of Unbalanced and the difference will be recorded. Magento will continue to use the old amount (since that is what you collected from the customer) and the AvaTax dashboard will show the correct amount to be paid to the respective jurisdiction.
*   If you've customized checkout at all, it is your responsibility to confirm the continued functionality of the changes to Onepage Checkout presented in this extension package to add Address Normalization. If you're using a completely custom checkout, you'll need to manually merge over these changes.
*   AvaTax will only accept an invoice ID and credit memo ID once per store. Thus if you're testing in environments that are capable of duplicating those values, be careful to increment those values in Magento or create new test stores in AvaTax.

## Common Problems

**Q:** I can see tax rates during checkout, but nothing shows up in my AvaTax dashboard.  
*A:* Most likely this is because the Magento cron is not running. For details on how to set up the cron, please see [magentocommerce.com](http://www.magentocommerce.com/wiki/how_to_setup_a_cron_job).

*Q:* When am getting a "Doc status invalid" error message when creating a new invoice or credit memo.  
*A:* Usually this is a problem when a system is attempting to commit to AvaTax a document with an ID that conflicts with one that's already there. Most of this time, this occurs when more than one installation of Magento with the AvaTax extension is communicating with a single "company" in the AvaTax account. This almost always happens in the build up to launch of a site as multiple systems are connecting to the same AvaTax "company" for testing.

*Q:* I think I have an old version of the One Pica AvaTax Magento Extension. What is the current version and how can I upgrade mine?  
*A:* The current version can be found on [Magento Connect](http://www.magentocommerce.com/extension/specs/1839/one-pica-avatax#specs). You can upgrade the extension via Magento Connect within your installation. If you have a legacy versions of Magento Professional or Magento Enterprise without Magento Connect, you can download the latest version of the extension from our website at [onepica.com/magento-extensions/avatax](http://www.onepica.com/magento-extensions/avatax/).

*Q:* I am getting tax rates in the cart and orders are showing up in the AvaTax Dashboard, but they are always in an uncommitted state.  
*A:* There is an option in the admin config section that would (correctly) make this happen. Check to see if admin condiguration option _Action_ (detailed [here](#connection-settings)) is set to "Enable: calculate tax, sumit data" instead of "Endable: calculate tax, submit data, and commit"

*Q:* When AvaTax extension is enabled, Manage tax zones and Rates options from Sales > Taxes, some Calculation Settings and Tax Classes from System > Configuration > Sales > Tax disappear from Magento admin.  
*A:* AvaTax extension hides these options "by design" as far as all tax calculation goes through AvaTax service rather than Magento. AvaTax extension overrides some default Magento functionality and requires certain values to be set in tax settings. Thus these options should be hidden to avoid confusing customer, when he tries to change settings but there is no effect on tax calculation results. If AvaTax extension is disabled for at least one store/website, then these options are available for this particular store/website.

_
