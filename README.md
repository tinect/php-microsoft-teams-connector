## PHP Microsoft Teams Connector

A PHP package to send notifications to Microsoft Teams by using "Incoming Webhook". The aim of this package is to create your own cards and simply send notifications to your desired channel. At the moment Microsoft Teams only support MessageCard format via Connectors. I will update the card format, when AdaptiveCard format is supported.


## Package Installation - Composer

You can install the package via composer:

```bash
composer require sebbmeyer/php-microsoft-teams-connector
```

### Usage

When you want to send a simple notification to you channel, you can easily create a SimpleCard and send it via the TeamConnector

```php
// create connector instance
$connector = new \Sebbmyr\Teams\TeamsConnector(<INCOMING_WEBHOOK_URL>);
// create card
$card  = new \Sebbmyr\Teams\Cards\SimpleCard(['title' => 'Simple card title', 'text' => 'Simple card text']);
// send card via connector
$connector->send($card);
```

### Custom card

You can use the provided **CustomCard** class and add a color, facts, images, an activity, actions or a summary to it.

```php
// create a custom card
$card  = new \Sebbmyr\Teams\Cards\CustomCard('Package update', 'A custom card class was added to the package.');
// add information
$card->setColor('01BC36')
    ->addFactsText('Supported PHP versions',['<= 5.4.0','7.x'])
    ->addFactsText('Unsupported PHP versions',['Before Version 5.4'])
    ->addAction('Visit Github repository','https://github.com/sebbmeyer/php-microsoft-teams-connector')
    ->addFacts('Facts Section',['Fact Name 1' => 'Fact Value 1','Fact Name 2' => 'Fact Value 2']);
```

Or you can create your own cards for every purpose you need, just extend the **AbstractCard** class and implement the `getMessage()` function. This is an example of a [Laravel Forge deployment card](https://github.com/sebbmeyer/laravel-teams-connector)

```php
\\ Sebbmyr\LaravelTeams\Cards\ForgeCard.php
public function getMessage()
{
    return [
        "@type" => "MessageCard",
        "@context" => "http://schema.org/extensions",
        "summary" => "Forge Card",
        "themeColor" => ($this->data["status"] === 'success') ? self::STATUS_SUCCESS : self::STATUS_ERROR,
        "title" => "Forge deployment message",
        "sections" => [
            [
                "activityTitle" => "",
                "activitySubtitle" => "",
                "activityImage" => "",
                "facts" => [
                    [
                        "name" => "Server:",
                        "value" => $this->data["server"]['name']
                    ],
                    [
                        "name" => "Site",
                        "value" => "[". $this->data["site"]["name"] ."](http://". $this->data["site"]["name"] .")"
                    ],                        [
                        "name" => "Commit hash:",
                        "value" => "[". $this->data["commit_hash"] ."](". $this->data["commit_url"] .")"
                    ],
                    [
                        "name" => "Commit message",
                        "value" => $this->data["commit_message"]
                    ]
                ],
                "text" => ($this->data["status"] === 'success') ? $this->data["commit_author"] ." deployed some fresh code!" : "Something went wrong :/"
            ]
        ]
    ];
}
```

### License

This PHP Microsoft Teams connector is open-sourced software licensed under the [MIT license](http://opensource.org/licenses/MIT)
