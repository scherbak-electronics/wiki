# Magento 2 snippets 
Try the following snippets to run in yor XDebug evaluation console on-the-fly,
while execution stopped at the breakpoint, press ALT+F8 to open evaluation console and paste
one-line snippet here.
> [!IMPORTANT]
> XDebug evaluation console doesn't allow to run multiple lines PHP expressions,
> you can run one line expressions only.
> So all of these snippets must be in one line

> [!WARNING]
> This code is a dirty hack in terms of Magento 2 development best practices. 
> Do not use this code in real applications. 
> This is for debugging purposes only!

<br>

#### Get current quote from session
```php
# Quote
\Magento\Framework\App\ObjectManager::getInstance()->get('\Magento\Checkout\Model\Session')->getQuote();

# Quote items array
\Magento\Framework\App\ObjectManager::getInstance()->get('\Magento\Checkout\Model\Session')->getQuote()->getItems()

# Quote item
\Magento\Framework\App\ObjectManager::getInstance()->get('\Magento\Checkout\Model\Session')->getQuote()->getItems()[0]

# Quote item product
\Magento\Framework\App\ObjectManager::getInstance()->get('\Magento\Checkout\Model\Session')->getQuote()->getItems()[0]->getProduct();
```
<br>

#### Get product by ID
```php
\Magento\Framework\App\ObjectManager::getInstance()->get(\Magento\Catalog\Model\ProductRepository::class)->getById(3114);
```
<br>

#### Search product using collection
```php
\Magento\Framework\App\ObjectManager::getInstance()->create(\Magento\Catalog\Model\Product::class)->getCollection()->addAttributeToSelect('*')->addAttributeToFilter('trade_in_type', ['like' => '%Eligibility-based%'])->getItems();
```
<br>

#### Get logged in customer
```php
\Magento\Framework\App\ObjectManager::getInstance()->get(\Magento\Customer\Model\Session::class)->getCustomer();
```
<br>

#### Login Customer by ID
```php
\Magento\Framework\App\ObjectManager::getInstance()->get(\Magento\Customer\Model\Session::class)->setCustomerById(CUSTOMER_ID); 
```
<br>

#### See All Plugins Loaded for a Given Method
Replace CLASS_NAME with the full class name you are inspecting and METHOD_NAME with the method name. This snippet provides insights into all plugins (before, around, after) affecting a method, which is invaluable for debugging issues related to plugins.
```php
\Magento\Framework\App\ObjectManager::getInstance()->get(CLASS_NAME::class); $plugins = \Magento\Framework\App\ObjectManager::getInstance()->get(\Magento\Framework\Interception\PluginList\PluginList::class)->getPlugins(CLASS_NAME, METHOD_NAME);
```
<br>

#### Check Request Parameters
```php
\Magento\Framework\App\ObjectManager::getInstance()->get(\Magento\Framework\App\Request\Http::class)->getParams();
```
<br>

#### Get the Current Store
```php
\Magento\Framework\App\ObjectManager::getInstance()->get(\Magento\Store\Model\StoreManagerInterface::class)->getStore();
```
<br>

#### Inspect the Layout Handle
```php
\Magento\Framework\App\ObjectManager::getInstance()->get(\Magento\Framework\View\LayoutInterface::class)->getUpdate()->getHandles();
```
<br>

#### Get Rules Applied in Catalog
```php
\Magento\Framework\App\ObjectManager::getInstance()->get(\Magento\CatalogRule\Model\Rule::class)->getRulesFromProduct(date(), WEBSITE_ID, CUSTOMER_GROUP_ID, PRODUCT_ID);
```
<br>

#### Rules Applied in Cart
```php
\Magento\Framework\App\ObjectManager::getInstance()->get(\Magento\Checkout\Model\Session::class)->getQuote()->getAppliedRuleIds();
``` 
<br>

#### Get Payment Methods Available Right Now in Checkout
```php
\Magento\Framework\App\ObjectManager::getInstance()->get(\Magento\Payment\Model\Config::class)->getActiveMethods();
```
<br>

#### Get All Listeners of a Given Event
```php
\Magento\Framework\App\ObjectManager::getInstance()->get(\Magento\Framework\Event\Manager::class)->getEventConfig('global', 'event_name')->getObservers();
```
<br>

#### Admin session
```php
json_encode( \Magento\Framework\App\ObjectManager::getInstance()->get(\Magento\Backend\Model\Auth\Session::class)->getData(), JSON_PRETTY_PRINT);
```
<br>

#### Admin session using session manager
```php
\Magento\Framework\App\ObjectManager::getInstance()->get(\Magento\Security\Model\AdminSessionsManager::class)->getCurrentSession()
```

## Breakpoints
Highlighting the most used breakpoints provides a direct approach to debug Magento 2 with Xdebug. 
These specific spots in Magento 2's structure help developers quickly find and solve problems. 


#### Break Just Before Area is Loaded
* File: `vendor/magento/framework/App/Area.php`
* Method: `load()` Place a breakpoint at the beginning.
#### Before Any Request is Processed
* File: `vendor/magento/framework/App/Http.php`
* Method: `launch()`
#### Before Homepage is Loaded
* File: `vendor/magento/module-cms/Controller/Index/Index.php`
* Method: `execute()`
#### Before Product Details are Loaded
* File: `vendor/magento/module-catalog/Controller/Product/View.php`
* Method: `execute()`
#### After Config is Loaded
* File: `vendor/magento/framework/App/Config/Initial.php`
* Method: `load()`
#### Before Any Redirect Happens
* File: `vendor/magento/framework/Controller/Result/Redirect.php`
* Method: `render()`
#### At the Moment Product is Added to Cart
* File: `vendor/magento/module-checkout/Controller/Cart/Add.php`
* Method: `execute()`
#### At the Moment When a Discount is Applied
* File: `vendor/magento/module-sales-rule/Model/Rule/Action/Discount/AbstractDiscount.php` Depending on the discount type. 
* Method: Various, depending on discount logic.
#### Before any collection load
Let's say you want to find out which db table is being accessed right now:
* Class: `\Magento\Framework\Model\ResourceModel\Db\Collection\AbstractCollection`
* Method: `_beforeLoad` Then open evaluation console (ALT+F8) and put this expression: `$this->getSelect()->getPart(\Zend_Db_Select::FROM)['main_table']['tableName']`
#### To Detect Recursive Event Loop
* File: `vendor/magento/framework/Event/Manager.php`
* Method: `dispatch()`
#### Before Any Adminhtml Action is Dispatched
* File: `vendor/magento/module-backend/App/Action/Plugin/Authentication.php`
* Method: `aroundDispatch()`
  Set a breakpoint at the beginning of the aroundDispatch() method. This plugin wraps around the dispatch method of all admin actions. It's responsible for authenticating users and enforcing ACL checks before proceeding with any admin action. By breaking here, you can observe the pre-dispatch state for any admin action, offering insights into the request handling and authentication process.
  Adding this breakpoint is particularly helpful for: Debugging Access Control: Understanding why certain users may not have access to specific sections of the admin panel. Inspecting Admin Request Flow: Seeing how requests are processed and routed within the adminhtml area. Modifying Pre-Dispatch Behavior: Implementing custom logic or modifications before any admin action takes effect.
#### Form Key validation
* Class: `\Magento\Framework\Data\Form\FormKey\Validator`
* Method: `validate()`

> [!CAUTION]
> YOU DO THIS AT YOUR OWN RISK  

