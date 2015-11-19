# Yii2 Cookie Language Selector

Yii2 language selector component based on cookies mechanism. This is very simple component which adds language switch functionality to your site. It's not a widget, it doesn't offers any GUI like [lajax/yii2-language-picker](https://github.com/lajax/yii2-language-picker). This may be useful because every real web-site has your own design and HTML markup. Some of web-sites uses `<select>`, some uses dropdown list showed on mouse hover in CSS, some uses HTML form, some uses ajax or `onclick` event which submits hidden POST form. With this component you're free to use any selection method. All you need is to make a handler to save new current language.

## Installation

Installation is very easy.

### 1. Install component itself

Execute following command in directory of your Yii2 application:
```
composer require gugglegum/yii2-cookie-language-selector
```
(you may need to use `composer.phar` instead of `composer`). This will install the `gugglegum/yii2-cookie-language-selector` component into your application.

### 2. Add component to your config

Go to your frontend's config add component config into `components` section:

```
        'cookieLanguageSelector' => [
            'class' => 'gugglegum\Yii2\Extension\CookieLanguageSelector\Component',
            'defaultLanguage' => 'en-US',
            'validLanguages' => ['en-US', 'ru-RU', 'de-DE'],
        ],
```

The `defaultLanguage` is language used by default before language selection. The `validLanguages` is the list of languages which can be selected. Additionally you may define name of cookie to store language (`cookieName`) and it's expiration period (`cookieExpire`). By default cookie name is "lang" and it's expires after 7776000 seconds (90 days).

### 3. Add component in bootstrap

Your application stores current language in `Yii::$app->language`. So you need to add this component to `bootstrap` section of your frontend's config to init this component at every application start. You should get something like this:

```
    'bootstrap' => ['log', 'cookieLanguageSelector'],
```

The initialization of `cookieLanguageSelector` will just set `Yii::$app->language` to language retrieved from component. Please use long language format like "en-US", not just "en".

### 4. Create POST form on your web-site

You should create HTML form with POST method or ajax with POST to send new language to server side. Your form may contain only `language` parameter. Additionally you may send `redirectTo` parameter with URL your user should be redirected after switching language (the same URL where he was before). The names of this parameters (`language` and `redirectTo`) is not predefined, it's up to you and used only in step 5.

### 5. Create handler action

Add to your `SiteController.php` following action-method:

```
    /**
     * Switches the language and redirects back
     */
    public function actionSwitchLanguage()
    {
        Yii::$app->cookieLanguageSelector->setLanguage(Yii::$app->request->post('language'));
        $this->redirect(Yii::$app->request->post('redirectTo', ['site/index']));
    }
```
