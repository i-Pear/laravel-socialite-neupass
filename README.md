# laravel-socialite-neupass

```composer require wuwx/laravel-socialite-neupass```

```php
<?php
use Laravel\Socialite\Facades\Socialite;
class LoginController extends Controller
{
    public function showLoginForm()
    {
        return redirect()->route("login.neupass");
    }
    public function redirectToProvider()
    {
        $redirect_url=url("/");
        
        // Store the previous location for redirect after login
        $previous = url()->previous('');
        if ($previous && $previous !== route("login.neupass")) {
            $isPreviousFromInstance = (strpos($previous, url('/')) === 0);
            // prevent other sites from using our CAS
            if ($isPreviousFromInstance) {
                $redirect_url=$previous;
            }
        }

        return Socialite::driver('neupass')
                        ->redirectUrl(route("login.neupass.callback").'?retUrl='.$redirect_url)
                        ->redirect();
    }
    public function handleProviderCallback(Request $request)
    {
        $user = Socialite::driver('neupass')->user();
        // do log in here
        
        return redirect($request->get("retUrl"));
    }
}
```

```php
Route::get('login/neupass', 'Auth\LoginController@redirectToProvider')->name("login.neupass");
Route::get('login/neupass/callback', 'Auth\LoginController@handleProviderCallback')->name("login.neupass.callback");
```
