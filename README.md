# php-functions [![Build Status](https://travis-ci.org/adityasetiono/php-functions.svg?branch=master)](https://travis-ci.org/adityasetiono/php-functions)
Function helpers for PHP

# Installation
To install it with composer:
```shell
composer require adityasetiono/php-functions
```

# Usage
Import needed functions, for example:
```php
use function adityasetiono\util\{deserialize, serialize, generate_alphanumeric};
```

## Random String Generator
And to use it:
```php
$string = generate_alphanumeric(5);

// => "aR4z0"
```

## Serialize / Deserialize
This is a function to serialize/deserialize an array to an object and vice versa. 
Because the library is built as an external function,
the class properties are required to have *getters* and *setters*.

A class example
```php
// User.php
class User {
    protected $firstName;
    protected $lastName;
    protected $email;
    protected $username;
    
    public function setFirstName(string $firstName): User
    {
        $this->firstName = $firstName;
        
        return $this;
    }
    
    public function getFirstName(): string
    {
        return $this->firstName;
    }
    
    public function setLastName(string $lastName): User
    {
        $this->lastName = $lastName;
        
        return $this;
    }
    
    public function getLastName(): string
    {
        return $this->lastName;
    }
    
    public function setEmail(string $email): User
    {
        $this->email = $email;
        
        return $this;
    }
    
    public function getEmail(): string
    {
        return $this->email;
    }
    
    public function setUsername(string $username): User
    {
        $this->username = $username;
        
        return $this;
    }
    
    public function getUsername(): string
    {
        return $this->username;
    }
}
```
### Serializer
```php
$user = serialize([
    'firstName' => 'John',
    'lastName' => 'Doe',
    'email' => 'john.doe@example.com',
    'username' => 'johndoe'
], User::class);
```
Result
```php
echo $user->getFirstName(); // John
echo $user->getEmail(); // john.doe@example.com
```
Serialized object are compatible with DoctrineORM Entities and can be persisted to database as is (Only tested with Doctrine so far, other ORM Entities need to be tested separately).
This is just a generic serializer to serialize an array to an object.

Here is an example of DoctrineORM Entity Serialization
```php
// User.php
use Doctrine\ORM\Mapping as ORM;
/**
 * @ORM\Entity
 * @ORM\Table(name="user")
 */
class User {
    
    /**
     * @ORM\Id
     * @ORM\Column(type="guid")
     * @ORM\GeneratedValue(strategy="UUID")
     */
    protected $uuid;
    
    /**
     * @ORM\Column(type="string", nullable=true)
     */
    protected $firstName;
    
    /**
     * @ORM\Column(type="string", nullable=true)
     */
    protected $lastName;
    
    /**
     * @ORM\Column(type="string", nullable=true)
     */
    protected $email;
    
    /**
     * @ORM\Column(type="string", nullable=true)
     */
    protected $username;
    
    public function getUuid(): string
    {
        return $this->uuid;
    }
    
    public function setFirstName(string $firstName): User
    {
        $this->firstName = $firstName;
        
        return $this;
    }
    
    public function getFirstName(): string
    {
        return $this->firstName;
    }
    
    public function setLastName(string $lastName): User
    {
        $this->lastName = $lastName;
        
        return $this;
    }
    
    public function getLastName(): string
    {
        return $this->lastName;
    }
    
    public function setEmail(string $email): User
    {
        $this->email = $email;
        
        return $this;
    }
    
    public function getEmail(): string
    {
        return $this->email;
    }
    
    public function setUsername(string $username): User
    {
        $this->username = $username;
        
        return $this;
    }
    
    public function getUsername(): string
    {
        return $this->username;
    }
}
```
### Serializer
Uuid is a primary key and auto-generated by the DB. 
```php
$user = serialize([
    'firstName' => 'John',
    'lastName' => 'Doe',
    'email' => 'john.doe@example.com',
    'username' => 'johndoe'
], User::class);
```
Persist to DB
```php
// obtaining the entity manager
$entityManager = EntityManager::create($conn, $config);
$entityManager->persist($user);
$entityManager->flush();
```
### Deserializer
Getting the object/entity from Database
```php
$uuid = 'e48a0ba1-1504-11e8-ae97-0649b2727d37'; // Hardcoded for display purposes
$user = $entityManager->find(User::class, $uuid);
```
Use the deserialize
```php
$array = deserialize($user); // empty option means complete deserialization by default.
echo json_encode($array);
```
Content of `$array` will be
```json
{
  "uuid": "e48a0ba1-1504-11e8-ae97-0649b2727d37",
  "firstName": "John",
  "lastName": "Doe",
  "email": "john.doe@example.com",
  "username": "johndoe"
}
```
Another example with custom fields. The second parameter accepts an associative array with key which will be the result's key and value is the property name.
The deserializer expects the value to be exactly the same as camelCase of the `getter` without `get`.
```php
$customArray = deserialize($user,[
    'first_name' => 'firstName,
    'surname' => 'lastName',
    'email' => 'email',
]);
echo json_encode($customArray);
```
Content of `$customArray` will be
```json
{
  "first_name": "John",
  "surname": "Doe",
  "email": "john.doe@example.com"
}
```
The deserializer should work with nested object and array of objects. Further example will be provided in the documentation. (It is available in the tests files).
# Issues
Feel free to use GitHub Issues or email directly to `winged.zach@gmail.com` as I started this library on my own. Collaborators are welcomed.