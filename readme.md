# Yii2 RSS extension

[Yii2](http://www.yiiframework.com) RSS extension adds RSS-feed to your site

This is a fork of https://github.com/zelenin/yii2-rss because the original author abandoned it and there was a bug with sending headers.

## Installation

### Composer

The preferred way to install this extension is through [Composer](http://getcomposer.org/).

Either run

```
php composer.phar require balrok/yii2-rss "~0.1"
```

or add

```
"balrok/yii2-rss": "~0.1"
```

to the require section of your ```composer.json```

## Usage

Add action to your controller:

```php
public function actionRss()
{
    $dataProvider = new ActiveDataProvider([
        'query' => Post::find()->with(['user']),
        'pagination' => [
            'pageSize' => 10
        ],
    ]);

    $response = Yii::$app->getResponse();
    $headers = $response->getHeaders();

    $headers->set('Content-Type', 'application/rss+xml; charset=utf-8');

    echo \balrok\yii\extensions\Rss\RssView::widget([
        'dataProvider' => $dataProvider,
        'channel' => [
            'title' => function ($widget, \balrok\Feed $feed) {
                    $feed->addChannelTitle(Yii::$app->name);
            },
            'link' => Url::toRoute('/', true),
            'description' => 'Posts ',
            'language' => function ($widget, \balrok\Feed $feed) {
                return Yii::$app->language;
            },
            'image'=> function ($widget, \balrok\Feed $feed) {
                $feed->addChannelImage('http://example.com/channel.jpg', 'http://example.com', 88, 31, 'Image description');
            },
        ],
        'items' => [
            'title' => function ($model, $widget, \balrok\Feed $feed) {
                    return $model->name;
                },
            'description' => function ($model, $widget, \balrok\Feed $feed) {
                    return StringHelper::truncateWords($model->content, 50);
                },
            'link' => function ($model, $widget, \balrok\Feed $feed) {
                    return Url::toRoute(['post/view', 'id' => $model->id], true);
                },
            'author' => function ($model, $widget, \balrok\Feed $feed) {
                    return $model->user->email . ' (' . $model->user->username . ')';
                },
            'guid' => function ($model, $widget, \balrok\Feed $feed) {
                    $date = \DateTime::createFromFormat('Y-m-d H:i:s', $model->updated_at);
                    return Url::toRoute(['post/view', 'id' => $model->id], true) . ' ' . $date->format(DATE_RSS);
                },
            'pubDate' => function ($model, $widget, \balrok\Feed $feed) {
                    $date = \DateTime::createFromFormat('Y-m-d H:i:s', $model->updated_at);
                    return $date->format(DATE_RSS);
                }
        ]
    ]);
}
```

## Authors

* [Aleksandr Zelenin](https://github.com/zelenin/), e-mail: [aleksandr@zelenin.me](mailto:aleksandr@zelenin.me)
* [Carl Mai](https://github.com/balrok/), e-mail: [carl.schoenbach@gmail.com](mailto:carl.schoenbach@gmail.com)
