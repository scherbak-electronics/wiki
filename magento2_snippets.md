# Magento 2 snippets 

> [!WARNING]
> This code is a dirty hack in terms of Magento 2 development best practices. 
> Do not use this code in real applications. 
> This is for debugging purposes only!


> [!CAUTION]
> YOU DO THIS AT YOUR OWN RISK  

> [!IMPORTANT]
> XDebug evaluation console doesn't allow to run multiple lines PHP expressions, 
> you can run one line expressions only. 
> So all of these snippets must be in one line.

Try the following snippets to run in yor XDebug evaluation console on-the-fly, 
while execution stopped at the breakpoint, press ALT+F8 to open evaluation console and paste
one-line snippet here.
Get current quote from session
```php
\Magento\Framework\App\ObjectManager::getInstance()->get('\Magento\Checkout\Model\Session')->getQuote();
\Magento\Framework\App\ObjectManager::getInstance()->get('\Magento\Checkout\Model\Session')->getQuote()->getItems()[2]->getProduct();
```
<br>

### Get product by ID

---
```php
\Magento\Framework\App\ObjectManager::getInstance()->get(\Magento\Catalog\Model\ProductRepository::class)->getById(3114);
```
<br>

### Search product using collection

---
```php
\Magento\Framework\App\ObjectManager::getInstance()->create(\Magento\Catalog\Model\Product::class)->getCollection()->addAttributeToSelect('*')->addAttributeToFilter('trade_in_type', ['like' => '%Eligibility-based%'])->getItems();
```
<br>

### Get logged in customer

---
Quickly view the customer data of the logged-in user.
```php
\Magento\Framework\App\ObjectManager::getInstance()->get(\Magento\Customer\Model\Session::class)->getCustomer();
```
<br>

### Login Customer by ID

---
Replace CUSTOMER_ID with the ID of the customer you want to simulate as logged in. Useful for testing customer-specific issues without needing their credentials.
```php
\Magento\Framework\App\ObjectManager::getInstance()->get(\Magento\Customer\Model\Session::class)->setCustomerById(CUSTOMER_ID); 
```
<br>

### See All Plugins Loaded for a Given Method

---
Replace CLASS_NAME with the full class name you are inspecting and METHOD_NAME with the method name. This snippet provides insights into all plugins (before, around, after) affecting a method, which is invaluable for debugging issues related to plugins.
```php
\Magento\Framework\App\ObjectManager::getInstance()->get(CLASS_NAME::class); $plugins = \Magento\Framework\App\ObjectManager::getInstance()->get(\Magento\Framework\Interception\PluginList\PluginList::class)->getPlugins(CLASS_NAME, METHOD_NAME);
```

<br>

### Additional Debugging Snippets

---
Beyond the common scenarios, here are a few more snippets that could prove useful during your debug sessions:
```php
# Check Request Parameters 
\Magento\Framework\App\ObjectManager::getInstance()->get(\Magento\Framework\App\Request\Http::class)->getParams();

# Get the Current Store 
\Magento\Framework\App\ObjectManager::getInstance()->get(\Magento\Store\Model\StoreManagerInterface::class)->getStore();

# Inspect the Layout Handle 
\Magento\Framework\App\ObjectManager::getInstance()->get(\Magento\Framework\View\LayoutInterface::class)->getUpdate()->getHandles();
```

<br>

### How to Get Rules Applied in Catalog

---
To see all the catalog price rules that are applied to a product, you can use:
```php
# Replace PRODUCT_ID with the ID of the product. 
\Magento\Framework\App\ObjectManager::getInstance()->get(\Magento\Catalog\Model\Product::class)->load(PRODUCT_ID);

# Ensure you have the correct $date, $websiteId, 
# and $customerGroupId for accurate context. 
\Magento\Framework\App\ObjectManager::getInstance()->get(\Magento\CatalogRule\Model\Rule::class)->getRulesFromProduct($date, $websiteId, $customerGroupId, $product->getId());
```
<br>

### Rules Applied in Cart
To list all sales rules applied in the cart, the following snippet can be handy:
```php
# This will give you a comma-separated list of rule IDs. 
# You can then load each rule by ID to inspect its details.
\Magento\Framework\App\ObjectManager::getInstance()->get(\Magento\Checkout\Model\Session::class)->getQuote()->getAppliedRuleIds();
```
 
<br>

### Get Payment Methods Available Right Now in Checkout
To retrieve a list of all available payment methods in the checkout process, use:
```php
// This will return an array of active payment method instances, 
// which can be iterated over to get details about each payment method. 
\Magento\Framework\App\ObjectManager::getInstance()->get(\Magento\Payment\Model\Config::class)->getActiveMethods();
```

<br>

### Get All Listeners of a Given Event
This snippet will give you details about all observers configured
to listen to this event across all scopes (global, frontend, adminhtml).
To find out which observers are listening to a specific event, you can do:
```php
// Replace 'event_name' with the actual name of the event you're interested in.
\Magento\Framework\App\ObjectManager::getInstance()->get(\Magento\Framework\Event\Manager::class)->getEventConfig('global', 'event_name')->getObservers();
```

<br>

## Adminhtml

```php
// Get admin session:
json_encode( \Magento\Framework\App\ObjectManager::getInstance()->get(\Magento\Backend\Model\Auth\Session::class)->getData(), JSON_PRETTY_PRINT);

// Get admin session using session manager:
\Magento\Framework\App\ObjectManager::getInstance()->get(\Magento\Security\Model\AdminSessionsManager::class)->getCurrentSession()

```

## Break it Down Like This!
about intercepting execution before any admin action is dispatched can be incredibly useful for debugging admin panel specific issues or understanding the flow of admin requests in Magento 2.

#### Break Just Before Area is Loaded
* File: vendor/magento/framework/App/Area.php 
* Method: load() Place a breakpoint at the beginning of the load() method. This allows you to pause execution right before Magento initializes an area (e.g., frontend, adminhtml). 
See When Custom Module Inserts a Plugin
* File: Your plugin's file within the custom module. 
* Method: The method your plugin is intercepting. Set a breakpoint at the beginning of your plugin method. Ensure your plugin is properly configured in your module's di.xml. 
Before Any Request is Processed
* File: vendor/magento/framework/App/Http.php 
* Method: launch() A breakpoint at the start of the launch() method will halt execution before Magento processes any request. 
Before Homepage is Loaded
* File: vendor/magento/module-cms/Controller/Index/Index.php 
* Method: execute() This controller is responsible for rendering the homepage. Place a breakpoint at the start of the execute() method. 
Before Product Details are Loaded
* File: vendor/magento/module-catalog/Controller/Product/View.php 
* Method: execute() To inspect the state before product details are loaded, place a breakpoint at the beginning of the execute() method in the product view controller. 
After Config is Loaded
* File: vendor/magento/framework/App/Config/Initial.php 
* Method: load() Setting a breakpoint at the end of the load() method allows you to inspect the application configuration right after it's loaded but before it's utilized by the system. 
Before Any Redirect Happens
* File: vendor/magento/framework/Controller/Result/Redirect.php 
* Method: render() To catch the application just before it performs a redirect, place a breakpoint at the beginning of the render() method. 
At the Moment Product is Added to Cart
* File: vendor/magento/module-checkout/Controller/Cart/Add.php 
* Method: execute() This is the method executed when a product is added to the cart. A breakpoint at the start allows you to inspect the process. 
At the Moment When a Discount is Applied
* File: Depending on the discount type, but typically within classes extending vendor/magento/module-sales-rule/Model/Rule/Action/Discount/AbstractDiscount.php 
* Method: Various, depending on discount logic. Breakpoints in these methods can help you debug how discounts are calculated and applied. 
To Detect Recursive Event Loop
* File: vendor/magento/framework/Event/Manager.php 
* Method: dispatch() Placing a breakpoint at the start of the dispatch() method allows you to inspect events as they are triggered. By monitoring the call stack, you can identify recursive event loops. 

#### Before Any Adminhtml Action is Dispatched
To inspect or modify the execution flow before any action within the Magento admin panel is dispatched, you should focus on the admin action controller's dispatch method. This approach gives you a vantage point for debugging admin routes, permissions, and pre-processing actions.
* File: vendor/magento/module-backend/App/Action/Plugin/Authentication.php
* Method: aroundDispatch()
  Set a breakpoint at the beginning of the aroundDispatch() method. This plugin wraps around the dispatch method of all admin actions. It's responsible for authenticating users and enforcing ACL checks before proceeding with any admin action. By breaking here, you can observe the pre-dispatch state for any admin action, offering insights into the request handling and authentication process.
  Adding this breakpoint is particularly helpful for:
* Debugging Access Control: Understanding why certain users may not have access to specific sections of the admin panel.
* Inspecting Admin Request Flow: Seeing how requests are processed and routed within the adminhtml area.
* Modifying Pre-Dispatch Behavior: Implementing custom logic or modifications before any admin action takes effect.
  Wrapping Up


```php
// Form Key validation
\Magento\Framework\Data\Form\FormKey\Validator::validate

// Break point To get table name of any db request which returns collection:
\Magento\Framework\Model\ResourceModel\Db\Collection\AbstractCollection::_beforeLoad
$this->getSelect()->getPart(\Zend_Db_Select::FROM)['main_table']['tableName']
```



# Open M2 admin login
```php
// We need file:
// vendor/magento/module-user/Model/User.php
// then scroll down to
\Magento\User\Model\User::authenticate 

// method and make it always return true:
if (!$result) {
    $this->unsetData();
}
return true;
//return $result;

// Then we need method
\Magento\User\Model\User::verifyIdentity

// and comment these lines:
//            if ($this->getIsActive() != '1') {
//                throw new AuthenticationException(
//                    __(
//                        'The account sign-in was incorrect or your account is disabled temporarily. '
//                        . 'Please wait and try again later.'
//                    )
//                );
//            }


// then we have to modify this method:
\Magento\Backend\App\Action\Plugin\Authentication::aroundDispatch

// This is a plugin which intercepts all admin actions before any other controller actions call
// like a middleware checking permissions to force load admin user in Xdebug eval popup:
\Magento\Framework\App\ObjectManager::getInstance()->create(\Magento\User\Model\User::class)->load(1);


// then set user auth session:
$this->_auth->getAuthStorage()->setUser($user);
$this->_auth->getAuthStorage()->processLogin();

// the place where you are log in:
\Magento\Backend\Model\Auth::login

// at the line:
$this->getAuthStorage()->processLogin();
$this->_eventManager->dispatch
(
    'backend_auth_user_login_success',
    ['user' => $this->getCredentialStorage()]
);

// The place where session expiration checking happens:
\Magento\Security\Model\AdminSessionInfo::isSessionExpired

// the place where admin user is logged in happens:
\Magento\Security\Model\AdminSessionInfo::isLoggedInStatus


// Normal login process:
 \Magento\Backend\App\Action\Plugin\Authentication::aroundDispatch   
 \Magento\Backend\App\Action\Plugin\Authentication::_processNotLoggedInUser
 \Magento\Backend\App\Action\Plugin\Authentication::_performLogin
 \Magento\Backend\Model\Auth\Interceptor::login
 \Magento\Backend\Model\Auth\Session::processLogin
 \Magento\Security\Model\Plugin\Auth::afterLogin
 \Magento\Security\Model\AdminSessionsManager::processLogin
 
// Any request after login:
 \Magento\Backend\App\Action\Plugin\Authentication::aroundDispatch
 \Magento\Security\Model\Plugin\AuthSession::aroundProlong 
 \Magento\Security\Model\AdminSessionsManager::processProlong
 \Magento\Backend\Model\Auth\Session::refreshAcl
```
