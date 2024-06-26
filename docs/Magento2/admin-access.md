# Open M2 admin login
it's about how to open access to Magento 2 admin panel during development on local environment.
(article is under construction..)
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

```json
{
    "user_id": "1",
    "firstname": "vadim",
    "lastname": "vadim",
    "email": "scherbak.electronics@gmail.com",
    "username": "admin",
    "password": "10f0f690d8b5cbc6697040bf08460301e1fa1a033685d92e032ebe6b11e4ba4d:WOaeJWKbbfu21CwPIowYxAkEEJ52ldE1:3_32_2_67108864",
    "created": "2023-05-22 21:46:23",
    "modified": "2024-02-07 13:56:46",
    "logdate": "2024-02-07 13:54:51",
    "lognum": "4",
    "reload_acl_flag": "0",
    "is_active": "1",
    "extra": {
        "configState": {
            "admin_security": "1",
            "admin_emails": "0",
            "admin_startup": "0",
            "admin_url": "0",
            "admin_dashboard": "0",
            "admin_grid": "0",
            "admin_captcha": "0",
            "admin_usage": "0"
        }
    },
    "rp_token": null,
    "rp_token_created_at": null,
    "interface_locale": "en_US",
    "failures_num": "0",
    "first_failure": null,
    "lock_expires": null,
    "tfa_providers": []
}
```