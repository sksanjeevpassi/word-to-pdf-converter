# word-to-pdf-converter

01 Run the following commands one by one:

composer require barryvdh/laravel-dompdf
composer require phpoffice/phpword

02 Register Service Provider In config/app.php

'providers' => [
 .....
 Barryvdh\DomPDF\ServiceProvider::class,
]

'aliases' => [
 .....
 'PDF' => Barryvdh\DomPDF\Facade::class,
]

03 Register Route In routes/api.php

Route::post('/convert-to-pdf', [ConverterController::class, 'convertToPdf']);

04 Create controller
php artisan make:controller ConverterController

