# OblioApi
 Oblio.eu API implementation for PHP
 
 Install using composer
 ```
 composer require obliosoftware/oblio-api
 ```

## Create invoice

```
$defaultData = array(
    'cif'                => '',
    'client'             => [
        'cif'           => '',
        'name'          => '',
        'rc'            => '',
        'code'          => '',
        'address'       => '',
        'state'         => '',
        'city'          => '',
        'country'       => '',
        'iban'          => '',
        'bank'          => '',
        'email'         => '',
        'phone'         => '',
        'contact'       => '',
        'vatPayer'      => '',
    ],
    'issueDate'          => 'yyyy-mm-dd',
    'dueDate'            => '',
    'deliveryDate'       => '',
    'collectDate'        => '',
    'seriesName'         => '',
    'collect'            => [],
    'referenceDocument'  => [],
    'language'           => 'RO',
    'precision'          => 2,
    'currency'           => 'RON',
    'products'           => [
        [
            'name'          => 'Abonament',
            'code'          => '',
            'description'   => '',
            'price'         => '100',
            'measuringUnit' => '',
            'currency'      => 'RON',
            'vatName'       => 'Normala',
            'vatPercentage' => 19,
            'vatIncluded'   => true,
            'quantity'      => 2,
            'productType'   => 'Serviciu',
        ]
    ],
    'issuerName'         => '',
    'issuerId'           => '',
    'noticeNumber'       => '',
    'internalNote'       => '',
    'deputyName'         => '',
    'deputyIdentityCard' => '',
    'deputyAuto'         => '',
    'selesAgent'         => '',
    'mentions'           => '',
    'value'              => 0,
    'workStation'        => 'Sediu',
    'useStock'           => 0,
);
try {
    $api = new OblioSoftware\Api($email, $secret);
    // create invoice:
    $result = $api->createInvoice($defaultData);
} catch (Exception $e) {
    // error handle
}
```

## Cancel invoice
```
try {
    $issuerCif = ''; // your company CIF
    $api = new OblioSoftware\Api($email, $secret);
    // cancel/restore document:
    $api->setCif($issuerCif);
    $result = $api->cancel('invoice', $seriesName, $number, true/false);
} catch (Exception $e) {
    // error handle
}
```

## Create custom AccessTokenHandler example
```
use OblioSoftware\AccessToken;
use OblioSoftware\AccessToken\HandlerInterface;

class CustomAccessTokenHandler implements HandlerInterface {
    private $cacheKey = 'oblio_access_token';
    
    public function get(): ?AccessToken
    {
        $data = Cache::get($this->cacheKey);
        if ($data !== null) {
            $accessToken = new AccessToken($data);
            if ($accessToken && $accessToken->request_time + $accessToken->expires_in > time()) {
                return $accessToken;
            }
        }
        return null;
    }
    
    public function set(AccessToken $accessToken): void
    {
        Cache::set($this->cacheKey, $accessToken->toArray());
    }
}
```
