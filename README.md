# Encoder

This package provides a pure PHP drop-in replacement for the `ext-soap` encoding logic.
It can be used as a driver so that you don't have to install PHP's soap extension on your machine anymore.

# Want to help out? 💚

- [Become a Sponsor](https://github.com/php-soap/.github/blob/main/HELPING_OUT.md#sponsor)
- [Let us do your implementation](https://github.com/php-soap/.github/blob/main/HELPING_OUT.md#let-us-do-your-implementation)
- [Contribute](https://github.com/php-soap/.github/blob/main/HELPING_OUT.md#contribute)
- [Help maintain these packages](https://github.com/php-soap/.github/blob/main/HELPING_OUT.md#maintain)

Want more information about the future of this project? Check out this list of the [next big projects](https://github.com/php-soap/.github/blob/main/PROJECTS.md) we'll be working on.

# Installation

```bash
composer require php-soap/encoding
```

## Example usage

```php
use Soap\Encoding\Driver;
use Soap\Encoding\EncoderRegistry;
use Soap\Engine\SimpleEngine;
use Soap\Psr18Transport\Psr18Transport;
use Soap\Wsdl\Loader\StreamWrapperLoader;
use Soap\WsdlReader\Locator\ServiceSelectionCriteria;
use Soap\WsdlReader\Model\Definitions\SoapVersion;
use Soap\WsdlReader\Wsdl1Reader;

// Loads the WSDL with the php-soap/wsdl-reader package:
$wsdl = (new Wsdl1Reader(new StreamWrapperLoader()))($wsdlLocation);

// Create an engine based on the encoding system that is provided by this package:
$engine = new SimpleEngine(
    Driver::createFromWsdl1(
        $wsdl,
        ServiceSelectionCriteria::defaults()
            ->withPreferredSoapVersion(SoapVersion::SOAP_12),
        EncoderRegistry::default(),
    ),
    Psr18Transport::createForClient($httpClient)
);

// Perform requests:
$decodedResult = $engine->request('Add', [
    [
        'a' => 1,
        'b' => 2
    ]
]);

/*
RESULT :

class stdClass#2135 (1) {
  public $AddResult =>
  int(3)
}
 */
```

## EncoderRegistry

The `EncoderRegistry` is a collection of encoders that can be used to encode and decode data.
By default, we provide a broad set of encoders to perform the basic soap encoding logic.
However, you can configure how the encoding should be done by adding your own encoders to the registry.

Some examples:

```php
use Soap\Encoding\EncoderRegistry;
use Soap\Xml\Xmlns;

EncoderRegistry::default()
    ->addClassMap('urn:namespace', 'TypeA', TypeA::class)
    ->addClassMap('urn:namespace', 'TypeB', TypeB::class)
    ->addBackedEnum('urn:namespace', 'EnumA', EnumA::class)
    ->addSimpleTypeConverter(Xmlns::xsd()->value(), 'dateTime', MyDateTimeEncoder::class)
    ->addComplexTypeConverter('urn:namespace', 'TypeC', MySpecificTypeCEncoder::class);
```
