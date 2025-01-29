# [Projet TDD](../readme.md)
# Guide des Tests Symfony

## Table des matières
- [Tests Basiques](#tests-basiques)
- [Mocks de Classes](#mocks-de-classes)
- [Mocks de Services](#mocks-de-services)
- [Tests avec Base de Données](#tests-avec-base-de-données)
- [Tests de Pages Web](#tests-de-pages-web)
- [Contextes Supplémentaires](#contextes-supplémentaires)

## Tests Basiques

### Test d'une méthode simple
```php
namespace App\Tests;

use PHPUnit\Framework\TestCase;
use App\Service\CalculatorService;

class CalculatorTest extends TestCase
{
    public function testAddition(): void
    {
        $calculator = new CalculatorService();
        $result = $calculator->add(2, 3);
        $this->assertEquals(5, $result);
    }
}
```

### Test avec assertion multiple
```php
public function testUserValidation(): void
{
    $user = new User();
    $user->setEmail('test@example.com')
         ->setUsername('testuser');

    $this->assertEquals('test@example.com', $user->getEmail());
    $this->assertEquals('testuser', $user->getUsername());
    $this->assertTrue($user->isActive());
}
```

## Mocks de Classes

### Test avec mock - Cas de succès
```php
public function testUserRepositoryFindSuccess(): void
{
    // Créer un mock de UserRepository
    $userRepository = $this->createMock(UserRepository::class);
    
    // Configurer le comportement attendu
    $userRepository->expects($this->once())
        ->method('find')
        ->with(1)
        ->willReturn(new User());

    $user = $userRepository->find(1);
    $this->assertInstanceOf(User::class, $user);
}
```

### Test avec mock - Cas d'échec
```php
public function testUserRepositoryFindFailure(): void
{
    $userRepository = $this->createMock(UserRepository::class);
    
    $userRepository->expects($this->once())
        ->method('find')
        ->with(999)
        ->willReturn(null);

    $user = $userRepository->find(999);
    $this->assertNull($user);
}
```

### Test avec mock - Résultats consecutifs
```php
public function testUserRepositoryFindSuccess(): void
{
    // Créer un mock de UserRepository
    $userRepository = $this->createMock(UserRepository::class);
    
    // Configurer le comportement attendu dans l'ordre de l'appel
    $userRepository->method('findBy')->willReturnOnConsecutiveCalls(
            [],
            array_fill(0, 15, true)
        );
    // Autre méthode
    $userRepository->->method('findBy')
                              ->willReturnCallback(function ($criteria) {
                                if ($criteria['ville'] === 'blois' ) {
                                    return array_fill(0, 2, true);
                                }
                                if ($criteria['ville'] === 'tours' ) {
                                    return array_fill(0, 3, true);
                                }
                                return array_fill(0, 5, true);
                              });


    $user = $userRepository->find(1);
    $this->assertInstanceOf(User::class, $user);
}
```

## Mocks de Services

### Test de service - Cas de succès
```php
public function testEmailServiceSuccess(): void
{
    // Mock du service d'envoi d'email
    $mailerService = $this->createMock(MailerInterface::class);
    
    $mailerService->expects($this->once())
        ->method('send')
        ->willReturn(true);

    $notificationService = new NotificationService($mailerService);
    $result = $notificationService->sendNotification('test@example.com', 'Test');
    
    $this->assertTrue($result);
}
```

### Test de service - Cas d'échec
```php
public function testEmailServiceFailure(): void
{
    $mailerService = $this->createMock(MailerInterface::class);
    
    $mailerService->expects($this->once())
        ->method('send')
        ->willThrowException(new \Exception('Failed to send email'));

    $notificationService = new NotificationService($mailerService);
    
    $this->expectException(\Exception::class);
    $notificationService->sendNotification('test@example.com', 'Test');
}
```
### Test d'un service qui instancie une classe
```php
namespace App\Tests\Service;

use PHPUnit\Framework\TestCase;
use App\Service\OrderProcessingService;
use App\Entity\Order;
use App\Notification\EmailNotifier;

class OrderProcessingServiceTest extends TestCase
{
    public function testProcessOrderWithMockedEmailNotifier(): void
    {
        // On crée un mock de EmailNotifier que le service va instancier
        $emailNotifierMock = $this->getMockBuilder(EmailNotifier::class)
            ->disableOriginalConstructor()
            ->getMock();
        
        // On configure le comportement attendu du mock
        $emailNotifierMock->expects($this->once())
            ->method('sendConfirmation')
            ->willReturn(true);
            
        // On utilise un mock builder pour remplacer la création de EmailNotifier
        // dans le service OrderProcessingService
        $orderProcessingService = $this->getMockBuilder(OrderProcessingService::class)
            ->onlyMethods(['createEmailNotifier'])
            ->getMock();
            
        // Quand createEmailNotifier() est appelé, retourne notre mock
        $orderProcessingService->method('createEmailNotifier')
            ->willReturn($emailNotifierMock);
            
        // On appelle la méthode à tester
        $order = new Order();
        $result = $orderProcessingService->processOrder($order);
        
        $this->assertTrue($result);
    }
}
```

La classe de service correspondante ressemblerait à ceci:

```php
namespace App\Service;

use App\Entity\Order;
use App\Notification\EmailNotifier;

class OrderProcessingService
{
    public function processOrder(Order $order): bool
    {
        // ... logique de traitement de la commande ...
        
        // Le service crée une instance de EmailNotifier
        $notifier = $this->createEmailNotifier();
        $notifier->sendConfirmation($order);
        
        return true;
    }
    
    // Méthode protégée que nous pouvons surcharger dans les tests
    protected function createEmailNotifier(): EmailNotifier
    {
        return new EmailNotifier();
    }
}
```

## Tests avec Base de Données

### Test d'insertion en base
```php
namespace App\Tests;

use App\Entity\Product;
use Symfony\Bundle\FrameworkBundle\Test\KernelTestCase;

class ProductTest extends KernelTestCase
{
    private $entityManager;

    protected function setUp(): void
    {
        $kernel = self::bootKernel();
        $this->entityManager = $kernel->getContainer()
            ->get('doctrine')
            ->getManager();
    }

    public function testCreateProduct(): void
    {
        $product = new Product();
        $product->setName('Test Product')
                ->setPrice(9.99);

        $this->entityManager->persist($product);
        $this->entityManager->flush();

        $savedProduct = $this->entityManager->getRepository(Product::class)
            ->findOneBy(['name' => 'Test Product']);

        $this->assertEquals('Test Product', $savedProduct->getName());
    }
}
```

### Test de requête complexe
```php
public function testFindActiveProducts(): void
{
    // Préparation des données de test
    $product1 = new Product();
    $product1->setName('Active Product')
             ->setActive(true);
    
    $product2 = new Product();
    $product2->setName('Inactive Product')
             ->setActive(false);

    $this->entityManager->persist($product1);
    $this->entityManager->persist($product2);
    $this->entityManager->flush();

    $activeProducts = $this->entityManager->getRepository(Product::class)
        ->findByActive(true);

    $this->assertCount(1, $activeProducts);
    $this->assertEquals('Active Product', $activeProducts[0]->getName());
}
```

## Tests de Pages Web

### Test de rendu de page
```php
namespace App\Tests\Controller;

use Symfony\Bundle\FrameworkBundle\Test\WebTestCase;

class HomeControllerTest extends WebTestCase
{
    public function testHomepage(): void
    {
        $client = static::createClient();
        $crawler = $client->request('GET', '/');

        $this->assertResponseIsSuccessful();
        $this->assertSelectorTextContains('h1', 'Bienvenue');
    }
}
```

### Test de formulaire
```php
public function testContactForm(): void
{
    $client = static::createClient();
    $crawler = $client->request('GET', '/contact');

    $form = $crawler->selectButton('Envoyer')->form();
    $form['contact[email]'] = 'test@example.com';
    $form['contact[message]'] = 'Test message';

    $client->submit($form);

    $this->assertResponseRedirects('/contact/success');
    $client->followRedirect();
    $this->assertSelectorTextContains('.alert-success', 'Message envoyé');
}
```

## Contextes Supplémentaires

### Test d'API
```php
public function testApiEndpoint(): void
{
    $client = static::createClient();
    $client->request('GET', '/api/products');

    $this->assertResponseHeaderSame('Content-Type', 'application/json');
    $this->assertResponseIsSuccessful();
    
    $response = json_decode($client->getResponse()->getContent(), true);
    $this->assertArrayHasKey('products', $response);
}
```

### Test avec authentification
```php
public function testSecuredPage(): void
{
    $client = static::createClient();
    $userRepository = static::getContainer()->get(UserRepository::class);
    
    $testUser = $userRepository->findOneByEmail('user@example.com');
    $client->loginUser($testUser);

    $client->request('GET', '/admin');
    
    $this->assertResponseIsSuccessful();
}
```

### Test avec événements
```php
public function testEventDispatch(): void
{
    $client = static::createClient();
    $eventDispatcher = static::getContainer()->get('event_dispatcher');

    $eventDispatched = false;
    $eventDispatcher->addListener('user.registered', function () use (&$eventDispatched) {
        $eventDispatched = true;
    });

    // Déclencher l'action qui devrait dispatcher l'événement
    $client->request('POST', '/register', [
        'email' => 'new@example.com',
        'password' => 'password123'
    ]);

    $this->assertTrue($eventDispatched, "L'événement user.registered n'a pas été dispatché");
}
```
