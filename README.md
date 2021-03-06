[![Build
Status](https://travis-ci.org/pravednik/xmlBundle.png?branch=master)](https://travis-ci.org/pravednik/xmlBundle)
[![SensioLabsInsight](https://insight.sensiolabs.com/projects/ac3fa776-bc19-4fd7-99f0-a0466c1b0bd3/mini.png)](https://insight.sensiolabs.com/projects/ac3fa776-bc19-4fd7-99f0-a0466c1b0bd3)
[![Latest Stable Version](https://poser.pugx.org/desperado/xml-bundle/v/stable.svg)](https://packagist.org/packages/desperado/xml-bundle)
[![License](https://poser.pugx.org/desperado/xml-bundle/license.svg)](https://packagist.org/packages/desperado/xml-bundle)
[![Total Downloads](https://poser.pugx.org/desperado/xml-bundle/downloads.svg)](https://packagist.org/packages/desperado/xml-bundle)

XML Builder/Reader Bundle for Symfony2

## Installation

Using composer

Add `desperado/xml-bundle` to your composer.json file.

```js
     "require": {
	     "desperado/xml-bundle": "dev-master"
     }
```
The next thing you should do is install the bundle by executing the following command:

    php composer.phar update desperado/xml-bundle

Finally, add the bundle to the registerBundles function of the AppKernel class in the _app/AppKernel.php_ file:

    public function registerBundles()
    {
        $bundles = array(
            ...
            new Desperado\XmlBundle\DesperadoXmlBundle,
            ...
        );

## Usage

### DIC

* XmlEditor: desperado_xml.model.xml_editor
* XmlGenerator: desperado_xml.model.xml_generator
* XmlReader: desperado_xml.model.xml_reader
* XmlPrepare: desperado_xml.model.xml_prepare

### Create xml from array

```php
<?php

use Desperado\XmlBundle\Model\XmlGenerator;

$params = [
            'Request' => [
                '@ns'               => [
                    '0_xmlns' => ['prefix' => 'xsi', 'uri' => 'http//www.w3.org/2001/XMLSchema-instance'],
                    '1_xmlns' => ['prefix' => 'xsd', 'uri' => 'http//www.w3.org/2001/XMLSchema'],
                ],
                '@attrib'           => [
                    'Id'      => 100,
                    'Service' => 200,
                    'xmlns'   => 'http://ekassir.com/ekassir/PaySystem/Server/eKassirV3Protocol'
                ],
                'PaymentParameters' => [
                    '@attrib'   => ['xmlns' => ''],
                    'Parameter' => [
                        '@attrib' => ['Name' => 'account'],
                        '@value'  => 'emptyAccount'
                    ]

                ]
            ]
        ];


        $xmlGenerator = new XmlGenerator;

        echo $xmlGenerator->generateFromArray($params);
```
prints:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<root xmlns:xsi="http//www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http//www.w3.org/2001/XMLSchema">
  <Request xmlns="http://ekassir.com/ekassir/PaySystem/Server/eKassirV3Protocol" xmlns:xsi="http//www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http//www.w3.org/2001/XMLSchema" Id="100" Service="200" xsi:xmlns="" xsd:xmlns="">
    <PaymentParameters xmlns="">
      <Parameter Name="account">emptyAccount</Parameter>
    </PaymentParameters>
  </Request>
</root>
```

### Create XML without attributes, namespaces, etc.

```php
<?php

use Desperado\XmlBundle\Model\XmlPrepare;
use Desperado\XmlBundle\Model\XmlGenerator;

        $params = [
            'Details' => [
                'PaymentParameters' => [
                    'first_node'  => 'first_node_value',
                    'second_node' => 'second_node_value'
                ]
            ]
        ];


        $xmlPrepare = new XmlPrepare;
        $xmlGenerator = new XmlGenerator;

        echo $xmlGenerator->setRootName('request')->generateFromArray($xmlPrepare->prepareArrayBeforeToXmlConvert($params));
```
prints:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<request>
  <Details>
    <PaymentParameters>
      <first_node>first_node_value</first_node>
      <second_node>second_node_value</second_node>
    </PaymentParameters>
  </Details>
</request>
```

### Parse XML without attributes, namespaces, etc.

```php
<?php

use Desperado\XmlBundle\Model\XmlReader;

        $xmlString = '<?xml version="1.0" encoding="UTF-8"?>
                      <request>
                          <Details>
                              <PaymentParameters>
                                  <first_node>first_node_value</first_node>
                                  <second_node>second_node_value</second_node>
                              </PaymentParameters>
                          </Details>
                      </request>';

        $xmlReader = new XmlReader;

        print_r($xmlReader->processConvert($xmlString));
```
prints:

```php
Array
(
    [Details] => Array
        (
            [PaymentParameters] => Array
                (
                    [first_node] => first_node_value
                    [second_node] => second_node_value
                )

        )

)
```
