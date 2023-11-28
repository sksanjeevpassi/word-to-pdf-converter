# Laravel Document to PDF Converter

This Laravel application provides an API to convert documents (DOC, DOCX) to PDF using the `barryvdh/laravel-dompdf` and `phpoffice/phpword` packages.

## Getting Started

Follow the steps below to set up and run the application:

# 1. Install Dependencies

Run the following commands one by one to install the required packages:

```bash
composer require barryvdh/laravel-dompdf
composer require phpoffice/phpword
```
# 2. Register Service Providers
In the config/app.php file, add the following lines to register the service providers:

```bash
'providers' => [
    // ...
    Barryvdh\DomPDF\ServiceProvider::class,
],

'aliases' => [
    // ...
    'PDF' => Barryvdh\DomPDF\Facade::class,
],
```
3. Register Route
In the routes/api.php file, register the conversion route:

```bash
use App\Http\Controllers\ConverterController;

Route::post('/convert-to-pdf', [ConverterController::class, 'convertToPdf']);
```

4. Create Controller
Generate a controller named ConverterController by running the following command:
```bash
php artisan make:controller ConverterController
```
```bash
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Support\Str;
use Illuminate\Support\Facades\File;

class ConverterController extends Controller
{

    public function convertToPdf(Request $request)
    {
        try {

            $this->validate($request, [
                'document' => 'required|mimes:doc,docx|max:10240'
            ]);

            // Set the PDF Engine Renderer Path
            $domPdfPath = base_path('vendor/dompdf/dompdf');
            \PhpOffice\PhpWord\Settings::setPdfRendererPath($domPdfPath);
            \PhpOffice\PhpWord\Settings::setPdfRendererName('DomPDF');

            // Load Word file
            $phpWord = \PhpOffice\PhpWord\IOFactory::load($request->file('document'));

            // Generate a unique filename for the PDF
            $filename = 'converted_' . Str::random(10) . '.pdf';

            // Create the 'pdf' directory dynamically if it doesn't exist
            $pdfDirectory = public_path('pdf');
            if (!File::isDirectory($pdfDirectory)) {
                File::makeDirectory($pdfDirectory, 0777, true, true);
            }

            // Save Word file as PDF
            $pdfPath = $pdfDirectory . '/' . $filename;
            $pdfWriter = \PhpOffice\PhpWord\IOFactory::createWriter($phpWord, 'PDF');
            $pdfWriter->save($pdfPath);

            // Return JSON response with the generated PDF URL
            $pdfUrl = asset('pdf/' . $filename);

            return response()->json(['status' => true, 'pdf_url' => $pdfUrl, 'message' => 'File has been successfully converted']);
        } catch (\Exception $e) {
            // Handle the exception
            return response()->json(['error' => $e->getMessage()], 500);
        }
    }
}
```
