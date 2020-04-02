# Universign Bundle

[![License](https://poser.pugx.org/globalis/universign-service/license)](https://packagist.org/packages/globalis/universign-service)


## Install


```yaml
#packages\universign.yaml
kiora_universign:
    path: api_path
    user: your_username
    password: your_pwd
```


## Usage

```php
<?php

use Globalis\Universign\Requester;
use Globalis\Universign\Request\{TransactionRequest, TransactionDocument, DocSignatureField, TransactionSigner};

class SomeController
{
    public function test(Requester $requester)
    {
        $signer1 = new TransactionSigner();
        $signer1
            ->setFirstname('Jean')
            ->setLastname('Dupond')
            ->setPhoneNum('0999999999')
            ->setEmailAddress('jean.dupond@example.com')
            ->setSuccessURL('https://www.universign.eu/fr/sign/success/')
            ->setCancelURL('https://www.universign.eu/fr/sign/cancel/')
            ->setFailURL('https://www.universign.eu/fr/sign/failed/')
            ->setProfile('profil_vendeur');
        
        $signers = [$signer1];

        $signatureField1 = new DocSignatureField();
        $signatureField1->setPage(1)
            ->setX(100)
            ->setY(200)
            ->setSignerIndex(0)
            ->setPatternName('default')
            ->setLabel("Sur l'ensemble du template 1");
        
        $signatureFieldsDoc1 = [$signatureField1];

        $doc1 = new TransactionDocument();
        $doc1->setPath('doc/Template_2.pdf')
            ->setSignatureFields($signatureFieldsDoc1);
        

        $request = new TransactionRequest();
        $request->addDocument($doc1)
            ->setSigners($signers)
            ->setHandwrittenSignatureMode(
                TransactionRequest::HANDWRITTEN_SIGNATURE_MODE_DIGITAL
            )
            ->setMustContactFirstSigner(false)
            ->setFinalDocRequesterSent(true)
            ->setChainingMode(
                TransactionRequest::CHAINING_MODE_WEB
            )
            ->setDescription("Demonstration de la signature Universign")
            ->setProfile("profile_demo")
            ->setCertificateTypes('simple')
            ->setLanguage('fr');

        $response = $requester->requestTransaction($request);

        $signatureUrl = $response->url;
        $transactionId = $response->id;

//....
    }
}