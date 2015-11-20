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

You should create HTML form with POST method or ajax with POST to send new language to the server. Your form may contain only `language` parameter. Additionally you may add `redirectTo` parameter with URL your user should be redirected after switching language (usually the same URL where user was before). The names of these parameters (`language` and `redirectTo`) is not predefined, it's completely up to you and they are used only in you handler action described in step 5.

Please avoid use GET method of HTTP for switching language. As described in RFC-2616 the GET method should not be used in case different from retrieving information. Your browser may prefetch links on your page and switch language randomly. On the other hand request with GET method can be cached on proxy, so switch may not work. Please always use POST method for action which doing something.

Here is very simple example of language switching form. Add this to some view or layout:

```
        <?= Html::beginForm(['site/switch-language'], 'post') ?>
        <?= Html::hiddenInput('redirectTo', \yii\helpers\Url::to(Yii::$app->request->url)) ?>
        <?= Html::beginTag('select', ['name' => 'language', 'onchange' => 'this.form.submit();']) ?>
        <?= Html::renderSelectOptions(\Yii::$app->language, [
            'en-US' => 'English',
            'de-DE' => 'Deutsch',
            'ru-RU' => 'Russian',
        ]) ?>
        <?= Html::endTag('select') ?>
        <?= Html::endForm() ?>
        <p>Current language is <?= Html::encode(\Yii::$app->language) ?> </p>
```

Of course you may use more customized variant. For example, you may use CSS stylized dropdown list which submits hidden POST form.

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
