# Class Diagrams

This document provides UML class diagrams for the Spring PetClinic application, organized by architectural layers.

## Core Domain Model

```mermaid
classDiagram
    class BaseEntity {
        -Integer id
        +getId() Integer
        +setId(Integer id) void
        +isNew() boolean
    }
    
    class Person {
        -String firstName
        -String lastName
        +getFirstName() String
        +setFirstName(String firstName) void
        +getLastName() String
        +setLastName(String lastName) void
    }
    
    class Owner {
        -String address
        -String city
        -String telephone
        -Set~Pet~ pets
        +getAddress() String
        +setAddress(String address) void
        +getCity() String
        +setCity(String city) void
        +getTelephone() String
        +setTelephone(String telephone) void
        +getPetsInternal() Set~Pet~
        +getPets() List~Pet~
        +addPet(Pet pet) void
        +getPet(String name) Pet
        +getPet(String name, boolean ignoreNew) Pet
    }
    
    class Pet {
        -String name
        -LocalDate birthDate
        -PetType type
        -Owner owner
        -Set~Visit~ visits
        +getName() String
        +setName(String name) void
        +getBirthDate() LocalDate
        +setBirthDate(LocalDate birthDate) void
        +getType() PetType
        +setType(PetType type) void
        +getOwner() Owner
        +setOwner(Owner owner) void
        +getVisitsInternal() Set~Visit~
        +getVisits() List~Visit~
        +addVisit(Visit visit) void
    }
    
    class PetType {
        -String name
        +getName() String
        +setName(String name) void
    }
    
    class Vet {
        -Set~Specialty~ specialties
        +getSpecialtiesInternal() Set~Specialty~
        +getSpecialties() List~Specialty~
        +getNrOfSpecialties() int
        +addSpecialty(Specialty specialty) void
    }
    
    class Specialty {
        -String name
        +getName() String
        +setName(String name) void
    }
    
    class Visit {
        -LocalDate date
        -String description
        -Pet pet
        +getDate() LocalDate
        +setDate(LocalDate date) void
        +getDescription() String
        +setDescription(String description) void
        +getPet() Pet
        +setPet(Pet pet) void
    }
    
    BaseEntity <|-- Person
    Person <|-- Owner
    Person <|-- Vet
    BaseEntity <|-- Pet
    BaseEntity <|-- PetType
    BaseEntity <|-- Specialty
    BaseEntity <|-- Visit
    
    Owner "1" --> "*" Pet : owns
    Pet "*" --> "1" PetType : is of type
    Pet "1" <-- "*" Visit : receives
    Vet "*" --> "*" Specialty : specializes in
```

## Repository Layer

```mermaid
classDiagram
    class Repository~T~ {
        <<interface>>
    }
    
    class CrudRepository~T, ID~ {
        <<interface>>
        +save(S entity) S
        +findById(ID id) Optional~T~
        +existsById(ID id) boolean
        +findAll() Iterable~T~
        +count() long
        +deleteById(ID id) void
        +delete(T entity) void
    }
    
    class OwnerRepository {
        <<interface>>
        +findByLastName(String lastName) Collection~Owner~
        +findAll() Collection~Owner~
        +findById(Integer id) Owner
        +save(Owner owner) void
    }
    
    class PetRepository {
        <<interface>>
        +findById(Integer id) Pet
        +save(Pet pet) void
    }
    
    class VetRepository {
        <<interface>>
        +findAll() Collection~Vet~
    }
    
    class VisitRepository {
        <<interface>>
        +findByPetId(Integer petId) List~Visit~
        +save(Visit visit) void
    }
    
    Repository <|-- CrudRepository
    CrudRepository <|-- OwnerRepository
    CrudRepository <|-- PetRepository
    CrudRepository <|-- VetRepository
    CrudRepository <|-- VisitRepository
```

## Service Layer

```mermaid
classDiagram
    class ClinicService {
        <<interface>>
        +findOwnerById(int id) Owner
        +findOwnerByLastName(String lastName) Collection~Owner~
        +saveOwner(Owner owner) void
        +findAllOwners() Collection~Owner~
        +findPetTypes() Collection~PetType~
        +findPetById(int id) Pet
        +savePet(Pet pet) void
        +findVets() Collection~Vet~
        +findVisitsByPetId(int petId) List~Visit~
        +saveVisit(Visit visit) void
    }
    
    class ClinicServiceImpl {
        -OwnerRepository owners
        -PetRepository pets
        -VetRepository vets
        -VisitRepository visits
        +findOwnerById(int id) Owner
        +findOwnerByLastName(String lastName) Collection~Owner~
        +saveOwner(Owner owner) void
        +findAllOwners() Collection~Owner~
        +findPetTypes() Collection~PetType~
        +findPetById(int id) Pet
        +savePet(Pet pet) void
        +findVets() Collection~Vet~
        +findVisitsByPetId(int petId) List~Visit~
        +saveVisit(Visit visit) void
    }
    
    ClinicService <|.. ClinicServiceImpl
```

## Controller Layer

```mermaid
classDiagram
    class OwnerController {
        -ClinicService clinicService
        +initCreationForm(Map model) Owner
        +processCreationForm(Owner owner, BindingResult result) String
        +initFindForm(Map model) Owner
        +processFindForm(Owner owner, BindingResult result, Map model) String
        +initUpdateOwnerForm(int ownerId, Model model) String
        +processUpdateOwnerForm(Owner owner, BindingResult result, int ownerId) String
        +showOwner(int ownerId, Model model) String
    }
    
    class PetController {
        -ClinicService clinicService
        +initCreationForm(Owner owner, Model model) Pet
        +processCreationForm(Owner owner, Pet pet, BindingResult result, Model model) String
        +initUpdateForm(int petId, Model model) String
        +processUpdateForm(Pet pet, BindingResult result, int petId, Model model) String
    }
    
    class VisitController {
        -ClinicService clinicService
        +initNewVisitForm(int petId, Map model) Visit
        +processNewVisitForm(Visit visit, BindingResult result) String
    }
    
    class VetController {
        -ClinicService clinicService
        +showVetList(Map model) String
        +showResourcesVetList() Vets
    }
    
    class CrashController {
        +triggerException() String
    }
    
    class WelcomeController {
        +welcome() String
    }
```

## REST API Controllers

```mermaid
classDiagram
    class OwnerResource {
        -ClinicService clinicService
        +findOwners() Collection~Owner~
        +findOwner(int ownerId) Owner
    }
    
    class PetResource {
        -ClinicService clinicService
        +findPet(int petId) Pet
    }
    
    class VetResource {
        -ClinicService clinicService
        +showResourcesVetList() Vets
    }
    
    class VisitResource {
        -ClinicService clinicService
        +findVisits(int petId) List~Visit~
    }
```

## Configuration Classes

```mermaid
classDiagram
    class PetClinicApplication {
        +main(String[] args) void
    }
    
    class CacheConfiguration {
        +cacheManager() CacheManager
    }
    
    class WebConfig {
        +setInternalResourceViewResolver() InternalResourceViewResolver
    }
```
