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
