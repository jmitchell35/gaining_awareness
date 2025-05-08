# Software Design Patterns: A Comprehensive Guide

## Table of Contents
- [Introduction](#introduction)
- [Creational Patterns](#creational-patterns)
- [Structural Patterns](#structural-patterns)
- [Behavioral Patterns](#behavioral-patterns)
- [Architectural Patterns](#architectural-patterns)
- [When to Use Design Patterns](#when-to-use-design-patterns)
- [Alternatives to Design Patterns](#alternatives-to-design-patterns)
- [Design Patterns in Web Applications](#design-patterns-in-web-applications)
- [Conclusion](#conclusion)

## Introduction

Design patterns are proven solutions to recurring problems in software design. They represent best practices that have evolved over time, helping developers create more maintainable, flexible, and robust code. This guide covers the main categories of design patterns, when to use them, and real-world examples to help you apply them in your projects.

As a beginner to programming working on an AirBnB-like web application, understanding these patterns will help you make better architectural decisions and communicate more effectively with other developers.

## Creational Patterns

Creational patterns focus on object creation mechanisms, trying to create objects in a manner suitable to the situation.

### Singleton

**Description**: Ensures a class has only one instance and provides a global point of access to it.

**When to use it**:
- When exactly one instance of a class is needed
- When the instance should be accessible from a well-known access point
- When the sole instance should be extensible

**Real-world examples**:
- Database connection pools
- Configuration managers
- Logging services

**Example implementation**:
```javascript
class DatabaseConnection {
  static instance;
  
  constructor() {
    // Database connection setup
  }
  
  static getInstance() {
    if (!DatabaseConnection.instance) {
      DatabaseConnection.instance = new DatabaseConnection();
    }
    return DatabaseConnection.instance;
  }
  
  query(sql) {
    // Execute query
  }
}

// Usage
const db1 = DatabaseConnection.getInstance();
const db2 = DatabaseConnection.getInstance();
console.log(db1 === db2); // true, same instance
```

**Pros**:
- Controlled access to sole instance
- Reduced namespace usage
- Allows refinement of operations and representation

**Cons**:
- Can make unit testing difficult
- Can be overused when global state isn't necessary

### Factory Method

**Description**: Defines an interface for creating an object, but lets subclasses decide which class to instantiate.

**When to use it**:
- When a class can't anticipate the type of objects it must create
- When a class wants its subclasses to specify the objects it creates
- When classes delegate responsibility to one of several helper subclasses

**Real-world examples**:
- Document generators (PDF, Word, Excel)
- UI component creation in frameworks
- Payment processor integrations

**Example implementation**:
```javascript
class PaymentProcessor {
  processPayment(amount) {
    const paymentMethod = this.createPaymentMethod();
    return paymentMethod.processPayment(amount);
  }
  
  createPaymentMethod() {
    throw new Error("Subclasses must implement createPaymentMethod");
  }
}

class StripeProcessor extends PaymentProcessor {
  createPaymentMethod() {
    return new StripePaymentMethod();
  }
}

class PayPalProcessor extends PaymentProcessor {
  createPaymentMethod() {
    return new PayPalPaymentMethod();
  }
}
```

**Pros**:
- Eliminates the need to bind application-specific classes to code
- Allows a class to defer instantiation to subclasses
- Connects parallel class hierarchies

**Cons**:
- Can lead to many small subclasses
- May be unnecessary for simple creation scenarios

### Abstract Factory

**Description**: Provides an interface for creating families of related or dependent objects without specifying their concrete classes.

**When to use it**:
- When a system needs to be independent from how its products are created
- When a system should be configured with one of multiple families of products
- When related product objects need to be used together

**Real-world examples**:
- UI toolkit implementations (Windows vs. macOS)
- Database access layers for different DBMS
- Vehicle manufacturing (cars, trucks with compatible parts)

**Example implementation**:
```javascript
// Abstract factory for UI components
class UIFactory {
  createButton() {}
  createInput() {}
}

class MaterialUIFactory extends UIFactory {
  createButton() {
    return new MaterialButton();
  }
  
  createInput() {
    return new MaterialInput();
  }
}

class Bootstrap5UIFactory extends UIFactory {
  createButton() {
    return new BootstrapButton();
  }
  
  createInput() {
    return new BootstrapInput();
  }
}

// Client code
function createUI(factory) {
  const button = factory.createButton();
  const input = factory.createInput();
  return { button, input };
}
```

**Pros**:
- Isolates concrete classes from client
- Makes exchanging product families easy
- Promotes consistency among products

**Cons**:
- Hard to support new kinds of products
- Can lead to unnecessary complexity for simple cases

### Builder

**Description**: Separates the construction of a complex object from its representation, allowing the same construction process to create different representations.

**When to use it**:
- When the algorithm for creating a complex object should be independent of the parts
- When the construction process must allow different representations for the object

**Real-world examples**:
- Document converters
- Meal ordering systems (like building a combo meal)
- Query builders in ORMs

**Example implementation**:
```javascript
class Listing {
  constructor() {
    this.features = [];
  }
}

class ListingBuilder {
  constructor() {
    this.listing = new Listing();
  }
  
  setTitle(title) {
    this.listing.title = title;
    return this;
  }
  
  setDescription(description) {
    this.listing.description = description;
    return this;
  }
  
  setPrice(price) {
    this.listing.price = price;
    return this;
  }
  
  addFeature(feature) {
    this.listing.features.push(feature);
    return this;
  }
  
  setLocation(lat, long) {
    this.listing.location = { lat, long };
    return this;
  }
  
  build() {
    return this.listing;
  }
}

// Usage
const listing = new ListingBuilder()
  .setTitle("Cozy Apartment")
  .setDescription("A nice apartment in downtown")
  .setPrice(100)
  .addFeature("WiFi")
  .addFeature("Kitchen")
  .setLocation(40.7128, -74.0060)
  .build();
```

**Pros**:
- Allows varying internal representation
- Isolates code for construction and representation
- Provides finer control over the construction process

**Cons**:
- Requires creating separate ConcreteBuilder for each different Product type
- Client code needs to be aware of specific builders

### Prototype

**Description**: Creates new objects by copying an existing object, known as the prototype.

**When to use it**:
- When a system should be independent of how its products are created
- When instances of a class can have one of only a few different combinations of state

**Real-world examples**:
- Cloning objects in graphic editors
- Creating copies of complex configurations
- JavaScript's prototypal inheritance

**Example implementation**:
```javascript
class PropertyTemplate {
  constructor(type, amenities, rules) {
    this.type = type;
    this.amenities = amenities;
    this.rules = rules;
  }
  
  clone() {
    return new PropertyTemplate(
      this.type,
      [...this.amenities],
      Object.assign({}, this.rules)
    );
  }
}

// Usage
const apartmentTemplate = new PropertyTemplate(
  'apartment',
  ['wifi', 'kitchen', 'ac'],
  { pets: false, smoking: false }
);

// Create new listing from template
const newListing = apartmentTemplate.clone();
newListing.rules.pets = true; // Customize
```

**Pros**:
- Adds and removes products at runtime
- Specifies new objects by varying values
- Reduces subclassing
- Configures application with classes dynamically

**Cons**:
- Each subclass must implement the clone operation
- Complex objects with circular references may be difficult to clone

## Structural Patterns

Structural patterns deal with object composition, creating relationships between objects to form larger structures.

### Facade

**Description**: Provides a unified interface to a set of interfaces in a subsystem, defining a higher-level interface that makes the subsystem easier to use.

**When to use it**:
- When you want to provide a simple interface to a complex subsystem
- When there are many dependencies between clients and implementation classes
- When you want to layer your subsystems

**Real-world examples**:
- Library APIs that simplify complex operations
- Payment processing systems
- AirBnB booking system (what you're working on!)

**Example implementation**:
```javascript
// Complex subsystems
class PropertySearch {
  findProperties(location, dates) {
    // Complex search logic
  }
}

class BookingManager {
  createBooking(propertyId, userId, dates) {
    // Complex booking logic
  }
}

class PaymentProcessor {
  processPayment(bookingId, paymentDetails) {
    // Complex payment logic
  }
}

class NotificationService {
  sendConfirmation(booking, user) {
    // Send emails, SMS, etc.
  }
}

// Facade
class BookingFacade {
  constructor() {
    this.propertySearch = new PropertySearch();
    this.bookingManager = new BookingManager();
    this.paymentProcessor = new PaymentProcessor();
    this.notificationService = new NotificationService();
  }
  
  bookProperty(userId, location, dates, paymentDetails) {
    // 1. Find available properties
    const properties = this.propertySearch.findProperties(location, dates);
    if (properties.length === 0) {
      return { success: false, message: "No properties available" };
    }
    
    // 2. Create booking
    const propertyId = properties[0].id;
    const booking = this.bookingManager.createBooking(propertyId, userId, dates);
    
    // 3. Process payment
    const paymentResult = this.paymentProcessor.processPayment(booking.id, paymentDetails);
    if (!paymentResult.success) {
      return { success: false, message: "Payment failed" };
    }
    
    // 4. Send confirmation
    this.notificationService.sendConfirmation(booking, { id: userId });
    
    return { 
      success: true, 
      booking: booking,
      message: "Booking confirmed" 
    };
  }
}

// Client code - simple!
const bookingFacade = new BookingFacade();
const result = bookingFacade.bookProperty(
  "user123",
  "New York",
  { checkIn: "2023-06-10", checkOut: "2023-06-15" },
  { cardNumber: "4111111111111111", expiryDate: "12/24" }
);
```

**Pros**:
- Shields clients from subsystem components
- Promotes loose coupling between subsystems and clients
- Reduces dependencies of outside code on inner workings of a library

**Cons**:
- Can become a "god object" coupled to all classes of an app
- May introduce an unnecessary layer if the subsystem is simple

### Adapter

**Description**: Converts the interface of a class into another interface clients expect, allowing classes to work together that couldn't otherwise.

**When to use it**:
- When you want to use an existing class, but its interface doesn't match what you need
- When you want to create a reusable class that cooperates with classes that don't have compatible interfaces
- When you need to use several existing subclasses but don't want to adapt each one individually

**Real-world examples**:
- Third-party payment gateway integrations
- Data format converters
- Legacy system integrations

**Example implementation**:
```javascript
// Existing class with incompatible interface
class ThirdPartyMapAPI {
  showLocation(longitude, latitude, zoom) {
    // Show a location on the map
    console.log(`Showing location at ${longitude}, ${latitude} with zoom ${zoom}`);
  }
}

// Target interface expected by client code
class MapService {
  displayLocation(location, zoomLevel) {
    throw new Error("Method not implemented");
  }
}

// Adapter
class ThirdPartyMapAdapter extends MapService {
  constructor() {
    super();
    this.thirdPartyMap = new ThirdPartyMapAPI();
  }
  
  displayLocation(location, zoomLevel) {
    // Adapt the call to the format expected by ThirdPartyMapAPI
    this.thirdPartyMap.showLocation(
      location.longitude,
      location.latitude,
      zoomLevel
    );
  }
}

// Client code
function showPropertyOnMap(mapService, property) {
  mapService.displayLocation(property.location, 15);
}

// Usage
const property = {
  name: "Beach House",
  location: { latitude: 34.0522, longitude: -118.2437 }
};

const mapService = new ThirdPartyMapAdapter();
showPropertyOnMap(mapService, property);
```

**Pros**:
- Allows incompatible interfaces to work together
- Reuses existing code
- Separates interface or data conversion code from business logic

**Cons**:
- Increases complexity by adding new classes
- Sometimes it's better to change the service class if you have access to its code

### Composite

**Description**: Composes objects into tree structures to represent part-whole hierarchies, letting clients treat individual objects and compositions uniformly.

**When to use it**:
- When you want to represent part-whole hierarchies of objects
- When you want clients to be able to ignore the differences between compositions of objects and individual objects

**Real-world examples**:
- File system structures (directories and files)
- UI components (containers and widgets)
- Organization charts

**Example implementation**:
```javascript
// Component interface
class Amenity {
  getName() {}
  getPrice() {}
}

// Leaf
class SingleAmenity extends Amenity {
  constructor(name, price) {
    super();
    this.name = name;
    this.price = price;
  }
  
  getName() {
    return this.name;
  }
  
  getPrice() {
    return this.price;
  }
}

// Composite
class AmenityPackage extends Amenity {
  constructor(name) {
    super();
    this.name = name;
    this.amenities = [];
  }
  
  add(amenity) {
    this.amenities.push(amenity);
  }
  
  remove(amenity) {
    const index = this.amenities.indexOf(amenity);
    if (index !== -1) {
      this.amenities.splice(index, 1);
    }
  }
  
  getName() {
    return this.name;
  }
  
  getPrice() {
    return this.amenities.reduce((sum, amenity) => sum + amenity.getPrice(), 0);
  }
}

// Usage
const wifi = new SingleAmenity('WiFi', 5);
const breakfast = new SingleAmenity('Breakfast', 15);
const parking = new SingleAmenity('Parking', 10);

const basicPackage = new AmenityPackage('Basic Package');
basicPackage.add(wifi);

const premiumPackage = new AmenityPackage('Premium Package');
premiumPackage.add(wifi);
premiumPackage.add(breakfast);
premiumPackage.add(parking);

console.log(`${basicPackage.getName()}: $${basicPackage.getPrice()}`);
console.log(`${premiumPackage.getName()}: $${premiumPackage.getPrice()}`);
```

**Pros**:
- Defines class hierarchies containing primitive and complex objects
- Makes client code simpler
- Makes it easier to add new types of components

**Cons**:
- Can make design overly general
- Can be difficult to restrict components of a composite to only certain types

### Decorator

**Description**: Attaches additional responsibilities to an object dynamically, providing a flexible alternative to subclassing for extending functionality.

**When to use it**:
- When you want to add responsibilities to objects dynamically without affecting other objects
- When extending functionality by subclassing is impractical
- When you want to add responsibilities that can be withdrawn

**Real-world examples**:
- User interface component styling and behaviors
- Stream classes with added functionality
- Feature flags in software products

**Example implementation**:
```javascript
// Basic component
class Listing {
  constructor(title, description, price) {
    this.title = title;
    this.description = description;
    this.price = price;
  }
  
  getDetails() {
    return {
      title: this.title,
      description: this.description,
      price: this.price
    };
  }
  
  getTotalPrice() {
    return this.price;
  }
}

// Decorator base class
class ListingDecorator {
  constructor(listing) {
    this.listing = listing;
  }
  
  getDetails() {
    return this.listing.getDetails();
  }
  
  getTotalPrice() {
    return this.listing.getTotalPrice();
  }
}

// Concrete decorators
class FeaturedListingDecorator extends ListingDecorator {
  getDetails() {
    const details = this.listing.getDetails();
    details.featured = true;
    return details;
  }
  
  getTotalPrice() {
    // Featured listings cost 20% more
    return this.listing.getTotalPrice() * 1.2;
  }
}

class SuperhostListingDecorator extends ListingDecorator {
  getDetails() {
    const details = this.listing.getDetails();
    details.superhost = true;
    return details;
  }
}

class DiscountedListingDecorator extends ListingDecorator {
  constructor(listing, discountPercentage) {
    super(listing);
    this.discountPercentage = discountPercentage;
  }
  
  getDetails() {
    const details = this.listing.getDetails();
    details.discount = this.discountPercentage;
    return details;
  }
  
  getTotalPrice() {
    return this.listing.getTotalPrice() * (1 - this.discountPercentage / 100);
  }
}

// Usage
let myListing = new Listing("Cozy Apartment", "Nice place in downtown", 100);
console.log(myListing.getDetails());
console.log("Total price: $" + myListing.getTotalPrice());

// Decorate the listing
myListing = new FeaturedListingDecorator(myListing);
myListing = new SuperhostListingDecorator(myListing);
console.log(myListing.getDetails());
console.log("Total price: $" + myListing.getTotalPrice());

// Add a discount for a special promotion
myListing = new DiscountedListingDecorator(myListing, 10);
console.log(myListing.getDetails());
console.log("Total price: $" + myListing.getTotalPrice());
```

**Pros**:
- More flexible than static inheritance
- Avoids feature-laden classes high up in the hierarchy
- Allows combining behaviors using multiple decorators
- Allows adding/removing responsibilities at runtime

**Cons**:
- Can result in many small objects that look similar
- Can be confusing for developers not familiar with the pattern

### Proxy

**Description**: Provides a surrogate or placeholder for another object to control access to it.

**When to use it**:
- When you need a more versatile or sophisticated reference to an object than a simple pointer
- When you want to add a layer of control over access to an object

**Real-world examples**:
- Lazy-loading of expensive resources
- Access control systems
- Caching mechanisms

**Example implementation**:
```javascript
// Service interface
class PropertyImageService {
  getImage(propertyId) {}
}

// Real service
class RealPropertyImageService extends PropertyImageService {
  getImage(propertyId) {
    // This might involve an expensive operation like a database query or API call
    console.log(`Fetching high-resolution image for property ${propertyId}`);
    return `high-res-image-${propertyId}.jpg`;
  }
}

// Proxy service with caching
class CachedPropertyImageService extends PropertyImageService {
  constructor() {
    super();
    this.realService = new RealPropertyImageService();
    this.cache = {};
  }
  
  getImage(propertyId) {
    if (!this.cache[propertyId]) {
      console.log(`Cache miss for property ${propertyId}`);
      this.cache[propertyId] = this.realService.getImage(propertyId);
    } else {
      console.log(`Cache hit for property ${propertyId}`);
    }
    
    return this.cache[propertyId];
  }
}

// Usage
const imageService = new CachedPropertyImageService();

// First time: cache miss
console.log(imageService.getImage("123"));

// Second time: cache hit
console.log(imageService.getImage("123"));

// Different property: cache miss
console.log(imageService.getImage("456"));
```

**Pros**:
- Controls access to the original object
- Can add functionality when accessing the object
- Can manage lifecycle of the original object when clients don't care

**Cons**:
- Adds another layer of indirection
- May introduce response delays

### Bridge

**Description**: Decouples an abstraction from its implementation so that the two can vary independently.

**When to use it**:
- When you want to avoid a permanent binding between an abstraction and its implementation
- When both the abstractions and implementations should be extensible through subclasses
- When changes in the implementation should not impact the client code

**Real-world examples**:
- GUI systems with different platform implementations
- Database drivers
- Device drivers

**Example implementation**:
```javascript
// Implementation interface
class PaymentProcessor {
  processPayment(amount) {}
}

// Concrete implementations
class StripePaymentProcessor extends PaymentProcessor {
  processPayment(amount) {
    console.log(`Processing $${amount} payment through Stripe`);
    // Stripe-specific logic
  }
}

class PayPalPaymentProcessor extends PaymentProcessor {
  processPayment(amount) {
    console.log(`Processing $${amount} payment through PayPal`);
    // PayPal-specific logic
  }
}

// Abstraction
class PaymentMethod {
  constructor(processor) {
    this.processor = processor;
  }
  
  pay(amount) {
    this.processor.processPayment(amount);
  }
}

// Refined abstractions
class CreditCardPayment extends PaymentMethod {
  pay(amount) {
    console.log("Using credit card payment method");
    this.processor.processPayment(amount);
  }
}

class BankTransferPayment extends PaymentMethod {
  pay(amount) {
    console.log("Using bank transfer payment method");
    this.processor.processPayment(amount);
  }
}

// Usage
const stripeProcessor = new StripePaymentProcessor();
const paypalProcessor = new PayPalPaymentProcessor();

const creditCardWithStripe = new CreditCardPayment(stripeProcessor);
const bankTransferWithPayPal = new BankTransferPayment(paypalProcessor);

creditCardWithStripe.pay(100);
bankTransferWithPayPal.pay(200);
```

**Pros**:
- Decouples interface from implementation
- Improves extensibility
- Hides implementation details from clients

**Cons**:
- Increases complexity for simple scenarios
- Can be confusing to some developers

### Flyweight

**Description**: Uses sharing to support large numbers of fine-grained objects efficiently.

**When to use it**:
- When you need a large number of similar objects
- When the storage cost of these objects is high
- When most of the object state can be made extrinsic

**Real-world examples**:
- Text editors sharing character objects
- Game engines reusing sprites
- Shared configuration objects

**Example implementation**:
```javascript
// Flyweight class - intrinsic state is shared
class PropertyTypeInfo {
  constructor(type, defaultAmenities, basePrice) {
    this.type = type;
    this.defaultAmenities = defaultAmenities;
    this.basePrice = basePrice;
  }
}

// Flyweight Factory
class PropertyTypeFactory {
  constructor() {
    this.propertyTypes = {};
  }
  
  getPropertyType(type) {
    if (!this.propertyTypes[type]) {
      // Create only if it doesn't exist
      switch(type) {
        case 'apartment':
          this.propertyTypes[type] = new PropertyTypeInfo(
            'apartment',
            ['wifi', 'kitchen'],
            100
          );
          break;
        case 'house':
          this.propertyTypes[type] = new PropertyTypeInfo(
            'house',
            ['wifi', 'kitchen', 'parking', 'garden'],
            200
          );
          break;
        case 'condo':
          this.propertyTypes[type] = new PropertyTypeInfo(
            'condo',
            ['wifi', 'kitchen', 'gym', 'pool'],
            150
          );
          break;
      }
    }
    
    return this.propertyTypes[type];
  }
}

// Client context class that uses the flyweight
class Property {
  constructor(id, name, type, location, host, factory) {
    this.id = id;
    this.name = name;
    this.location = location;
    this.host = host;
    // Get flyweight object with shared data
    this.propertyTypeInfo = factory.getPropertyType(type);
  }
  
  getDetails() {
    return {
      id: this.id,
      name: this.name,
      type: this.propertyTypeInfo.type,
      amenities: this.propertyTypeInfo.defaultAmenities,
      basePrice: this.propertyTypeInfo.basePrice,
      location: this.location,
      host: this.host
    };
  }
}

// Usage
const factory = new PropertyTypeFactory();

// Create thousands of properties without duplicating shared data
const properties = [
  new Property(1, "Cozy Downtown Apt", "apartment", "New York", "John", factory),
  new Property(2, "Beach House", "house", "Miami", "Lisa", factory),
  new Property(3, "Luxury Condo", "condo", "Los Angeles", "Mike", factory),
  new Property(4, "Modern Apt", "apartment", "Chicago", "Sarah", factory),
  // Imagine thousands more properties...
];

console.log(properties[0].getDetails());
console.log(properties[1].getDetails());
```

**Pros**:
- Reduces memory usage when many similar objects are used
- Externalizes object state when appropriate
- Can significantly improve application performance

**Cons**:
- Can introduce complexity
- May trade CPU cycles for memory if context switching is expensive

## Behavioral Patterns

Behavioral patterns focus on communication between objects, how they operate and assign responsibilities.

### Observer

**Description**: Defines a one-to-many dependency between objects so that when one object changes state, all its dependents are notified and updated automatically.

**When to use it**:
- When a change to one object requires changing others, and you don't know how many objects need to change
- When an object should be able to notify other objects without making assumptions about who these objects are

**Real-world examples**:
- Event handling systems
- Newsletter subscriptions
- Real-time data updates (like in a booking system)

**Example implementation**:
```javascript
// Subject interface
class Subject {
  subscribe(observer) {}
  unsubscribe(observer) {}
  notify() {}
}

// Concrete subject
class PropertyAvailability extends Subject {
  constructor(propertyId) {
    super();
    this.propertyId = propertyId;
    this.observers = [];
    this.isAvailable = true;
  }
  
  subscribe(observer) {
    this.observers.push(observer);
  }
  
  unsubscribe(observer) {
    this.observers = this.observers.filter(obs => obs !== observer);
  }
  
  notify() {
    for (const observer of this.observers) {
      observer.update(this);
    }
  }
  
  setAvailability(isAvailable) {
    if (this.isAvailable !== isAvailable) {
      this.isAvailable = isAvailable;
      this.notify();
    }
  }
  
  getAvailability() {
    return this.isAvailable;
  }
}

// Observer interface
class Observer {
  update(subject) {}
}

// Concrete observers
class UserNotifier extends Observer {
  constructor(userId) {
    super();
    this.userId = userId;
  }
  
  update(subject) {
    if (subject.getAvailability()) {
      console.log(`Notifying user ${this.userId}: Property ${subject.propertyId} is now available!`);
      // Send email, notification, etc.
    }
  }
}

class AnalyticsTracker extends Observer {
  update(subject) {
    console.log(`Tracking availability change for property ${subject.propertyId}: ${subject.getAvailability()}`);
    // Log analytics data
  }
}

// Usage
const property = new PropertyAvailability("123");

const user1 = new UserNotifier("user1");
const user2 = new UserNotifier("user2");
const analytics = new AnalyticsTracker();

property.subscribe(user1);
property.subscribe(user2);
property.subscribe(analytics);

// Property becomes unavailable
property.setAvailability(false);

// User2 unsubscribes
property.unsubscribe(user2);

// Property becomes available again
property.setAvailability(true);
```

**Pros**:
- Supports loose coupling between objects
- Allows sending data to many objects efficiently
- Dynamic relationships can be created at runtime

**Cons**:
- Unexpected updates can cascade and be hard to track
- If not careful, can introduce memory leaks (forgotten subscriptions)

### Strategy

**Description**: Defines a family of algorithms, encapsulates each one, and makes them interchangeable. Strategy lets the algorithm vary independently from clients that use it.

**When to use it**:
- When you want to define a class that will have one behavior that is similar to other behaviors in a list
- When you need different variants of an algorithm
- When an algorithm uses data that clients shouldn't know about

**Real-world examples**:
- Sorting algorithms
- Payment processing strategies
- Compression algorithms

**Example implementation**:
```javascript
// Strategy interface
class PricingStrategy {
  calculatePrice(basePricePerNight, nights, property) {}
}

// Concrete strategies
class RegularPricingStrategy extends PricingStrategy {
  calculatePrice(basePricePerNight, nights, property) {
    return basePricePerNight * nights;
  }
}

class WeekendPricingStrategy extends PricingStrategy {
  calculatePrice(basePricePerNight, nights, property) {
    // Higher price for weekends
    return basePricePerNight * nights * 1.5;
  }
}

class SeasonalPricingStrategy extends PricingStrategy {
  calculatePrice(basePricePerNight, nights, property) {
    // Price varies by season
    const season = this.getCurrentSeason();
    let multiplier = 1;
    
    switch(season) {
      case 'summer':
        multiplier = 1.8;
        break;
      case 'winter':
        multiplier = 1.2;
        break;
      case 'fall':
      case 'spring':
        multiplier = 1.4;
        break;
    }
    
    return basePricePerNight * nights * multiplier;
  }
  
  getCurrentSeason() {
    const month = new Date().getMonth();
    if (month >= 5 && month <= 7) return 'summer';
    if (month >= 8 && month <= 10) return 'fall';
    if (month >= 11 || month <= 1) return 'winter';
    return 'spring';
  }
}

// Context
class Booking {
  constructor(property, pricingStrategy) {
    this.property = property;
    this.pricingStrategy = pricingStrategy;
  }
  
  setPricingStrategy(pricingStrategy) {
    this.pricingStrategy = pricingStrategy;
  }
  
  calculateTotalPrice(nights) {
    return this.pricingStrategy.calculatePrice(
      this.property.basePricePerNight,
      nights,
      this.property
    );
  }
}

// Usage
const property = {
  id: "123",
  name: "Beach House",
  basePricePerNight: 100
};

// Create a booking with regular pricing
const booking = new Booking(property, new RegularPricingStrategy());
console.log(`Regular price for 5 nights: $${booking.calculateTotalPrice(5)}`);

// Switch to weekend pricing
booking.setPricingStrategy(new WeekendPricingStrategy());
console.log(`Weekend price for 2 nights: $${booking.calculateTotalPrice(2)}`);

// Switch to seasonal pricing
booking.setPricingStrategy(new SeasonalPricingStrategy());
console.log(`Seasonal price for 7 nights: $${booking.calculateTotalPrice(7)}`);
```

**Pros**:
- Algorithms can be swapped at runtime
- Isolates algorithm implementation details from code that uses it
- Eliminates conditional statements
- Easier to extend and incorporate new strategies

**Cons**:
- Clients must be aware of different strategies
- Increases number of objects in the application

### Command

**Description**: Encapsulates a request as an object, allowing you to parameterize clients with different requests, queue or log requests, and support undoable operations.

**When to use it**:
- When you want to parameterize objects with operations
- When you want to queue, specify, and execute requests at different times
- When you want to support undo operations

**Real-world examples**:
- GUI buttons and menu items
- Task scheduling systems
- Transaction systems with rollback

**Example implementation**:
```javascript
// Command interface
class Command {
  execute() {}
  undo() {}
}

// Concrete commands
class BookPropertyCommand extends Command {
  constructor(bookingService, propertyId, userId, dates) {
    super();
    this.bookingService = bookingService;
    this.propertyId = propertyId;
    this.userId = userId;
    this.dates = dates;
    this.bookingId = null;
  }
  
  execute() {
    this.bookingId = this.bookingService.bookProperty(
      this.propertyId,
      this.userId,
      this.dates
    );
    return this.bookingId;
  }
  
  undo() {
    if (this.bookingId) {
      this.bookingService.cancelBooking(this.bookingId);
      console.log(`Booking ${this.bookingId} cancelled`);
      this.bookingId = null;
    }
  }
}

class ApplyDiscountCommand extends Command {
  constructor(bookingService, bookingId, discountPercent) {
    super();
    this.bookingService = bookingService;
    this.bookingId = bookingId;
    this.discountPercent = discountPercent;
    this.originalPrice = null;
  }
  
  execute() {
    this.originalPrice = this.bookingService.getBookingPrice(this.bookingId);
    this.bookingService.applyDiscount(this.bookingId, this.discountPercent);
    const newPrice = this.bookingService.getBookingPrice(this.bookingId);
    console.log(`Discount applied: $${this.originalPrice} -> $${newPrice}`);
    return newPrice;
  }
  
  undo() {
    if (this.originalPrice) {
      this.bookingService.setBookingPrice(this.bookingId, this.originalPrice);
      console.log(`Discount removed, price restored to $${this.originalPrice}`);
    }
  }
}

// Receiver
class BookingService {
  constructor() {
    this.bookings = {};
    this.nextBookingId = 1;
  }
  
  bookProperty(propertyId, userId, dates) {
    const bookingId = this.nextBookingId++;
    this.bookings[bookingId] = {
      propertyId,
      userId,
      dates,
      price: 100 // Default price
    };
    console.log(`Property ${propertyId} booked for user ${userId}`);
    return bookingId;
  }
  
  cancelBooking(bookingId) {
    if (this.bookings[bookingId]) {
      delete this.bookings[bookingId];
      return true;
    }
    return false;
  }
  
  getBookingPrice(bookingId) {
    return this.bookings[bookingId]?.price || 0;
  }
  
  setBookingPrice(bookingId, price) {
    if (this.bookings[bookingId]) {
      this.bookings[bookingId].price = price;
      return true;
    }
    return false;
  }
  
  applyDiscount(bookingId, discountPercent) {
    if (this.bookings[bookingId]) {
      const booking = this.bookings[bookingId];
      booking.price = booking.price * (1 - (discountPercent / 100));
      return true;
    }
    return false;
  }
}

// Invoker
class BookingManager {
  constructor() {
    this.commandHistory = [];
  }
  
  executeCommand(command) {
    const result = command.execute();
    this.commandHistory.push(command);
    return result;
  }
  
  undoLastCommand() {
    const command = this.commandHistory.pop();
    if (command) {
      command.undo();
      return true;
    }
    return false;
  }
}

// Usage
const bookingService = new BookingService();
const bookingManager = new BookingManager();

// Execute booking command
const bookCommand = new BookPropertyCommand(
  bookingService,
  "property123",
  "user456",
  { checkIn: "2023-06-10", checkOut: "2023-06-15" }
);
const bookingId = bookingManager.executeCommand(bookCommand);

// Apply discount
const discountCommand = new ApplyDiscountCommand(
  bookingService,
  bookingId,
  20
);
bookingManager.executeCommand(discountCommand);

// Undo the discount (last command)
bookingManager.undoLastCommand();

// Undo the booking
bookingManager.undoLastCommand();
```

**Pros**:
- Decouples objects that invoke operations from objects that perform them
- Allows assembling commands into a composite command
- Can implement undo/redo functionality
- Supports transaction-like behavior

**Cons**:
- Increases the number of classes for each command
- May be overkill for simple applications

### Template Method

**Description**: Defines the skeleton of an algorithm in a method, deferring some steps to subclasses. It lets subclasses redefine certain steps of an algorithm without changing the algorithm's structure.

**When to use it**:
- When you want to implement the invariant parts of an algorithm once and leave the variable parts to subclasses
- When common behavior among subclasses should be factored out to avoid code duplication

**Real-world examples**:
- Data processing pipelines
- Workflow engines
- Framework initialization processes

**Example implementation**:
```javascript
// Abstract class with template method
class ListingCreationProcess {
  // Template method
  createListing(listingData) {
    this.validateData(listingData);
    const listingId = this.saveBasicInfo(listingData);
    this.processImages(listingId, listingData.images);
    this.setAvailability(listingId, listingData.availability);
    this.setPricing(listingId, listingData.pricing);
    this.additionalProcessing(listingId, listingData);
    this.publishListing(listingId);
    return listingId;
  }
  
  // Methods that subclasses might override
  validateData(listingData) {
    // Basic validation logic
    const requiredFields = ['title', 'description', 'location'];
    for (const field of requiredFields) {
      if (!listingData[field]) {
        throw new Error(`Missing required field: ${field}`);
      }
    }
  }
  
  saveBasicInfo(listingData) {
    // Save to database and return ID
    console.log("Saving basic listing info");
    return "listing-" + Math.floor(Math.random() * 1000);
  }
  
  processImages(listingId, images) {
    // Default implementation
    console.log(`Processing ${images?.length || 0} images for listing ${listingId}`);
  }
  
  setAvailability(listingId, availability) {
    console.log(`Setting availability for listing ${listingId}`);
  }
  
  setPricing(listingId, pricing) {
    console.log(`Setting basic pricing for listing ${listingId}`);
  }
  
  // Hook - empty by default, subclasses can override
  additionalProcessing(listingId, listingData) {
    // Do nothing by default
  }
  
  publishListing(listingId) {
    console.log(`Publishing listing ${listingId}`);
  }
}

// Concrete implementations
class ApartmentListingProcess extends ListingCreationProcess {
  validateData(listingData) {
    super.validateData(listingData);
    
    // Additional apartment-specific validation
    if (!listingData.floor) {
      throw new Error("Apartments must specify the floor number");
    }
  }
  
  processImages(listingId, images) {
    console.log(`Processing and verifying floor plan for apartment ${listingId}`);
    super.processImages(listingId, images);
  }
  
  additionalProcessing(listingId, listingData) {
    console.log(`Adding apartment-specific amenities for listing ${listingId}`);
  }
}

class VacationHomeListingProcess extends ListingCreationProcess {
  setPricing(listingId, pricing) {
    console.log(`Setting seasonal pricing variations for vacation home ${listingId}`);
  }
  
  additionalProcessing(listingId, listingData) {
    console.log(`Adding nearby attractions for vacation home ${listingId}`);
  }
}

// Usage
const apartmentData = {
  title: "Downtown Studio",
  description: "Cozy studio in the heart of the city",
  location: "New York",
  floor: 3,
  images: ["image1.jpg", "image2.jpg"],
  availability: { startDate: "2023-06-01", endDate: "2023-12-31" },
  pricing: { basePrice: 100, cleaningFee: 25 }
};

const vacationHomeData = {
  title: "Beach House",
  description: "Perfect getaway by the ocean",
  location: "Miami",
  images: ["image1.jpg", "image2.jpg", "image3.jpg"],
  availability: { startDate: "2023-06-01", endDate: "2023-12-31" },
  pricing: { basePrice: 200, cleaningFee: 50, seasonalMultipliers: { summer: 1.5, winter: 1.2 } }
};

const apartmentProcess = new ApartmentListingProcess();
const vacationHomeProcess = new VacationHomeListingProcess();

console.log("Creating apartment listing:");
const apartmentId = apartmentProcess.createListing(apartmentData);

console.log("\nCreating vacation home listing:");
const vacationHomeId = vacationHomeProcess.createListing(vacationHomeData);
```

**Pros**:
- Reuses code among subclasses
- Allows factoring out common behavior
- Provides hooks for subclasses to extend behavior

**Cons**:
- May violate the "prefer composition over inheritance" principle
- Can be difficult to understand the flow of control
- Can lead to tight coupling if subclasses have to interact with the template method

### State

**Description**: Allows an object to alter its behavior when its internal state changes. The object will appear to change its class.

**When to use it**:
- When an object's behavior depends on its state, and it must change behavior at runtime depending on that state
- When operations have large, multipart conditional statements that depend on the object's state

**Real-world examples**:
- Order processing states (new, paid, shipped, delivered)
- Document approval workflows
- Media players (playing, paused, stopped)

**Example implementation**:
```javascript
// State interface
class BookingState {
  constructor(booking) {
    this.booking = booking;
  }
  
  requestToBook() {}
  pay() {}
  cancel() {}
  checkIn() {}
  checkOut() {}
  review() {}
}

// Concrete states
class PendingState extends BookingState {
  requestToBook() {
    console.log("Booking request already sent, waiting for approval");
  }
  
  pay() {
    console.log("Cannot pay for a booking that hasn't been approved yet");
  }
  
  cancel() {
    console.log("Cancelling pending booking request");
    this.booking.setState(new CancelledState(this.booking));
  }
  
  checkIn() {
    console.log("Cannot check in to a pending booking");
  }
  
  checkOut() {
    console.log("Cannot check out of a pending booking");
  }
  
  review() {
    console.log("Cannot review a pending booking");
  }
}

class ConfirmedState extends BookingState {
  requestToBook() {
    console.log("Booking already confirmed");
  }
  
  pay() {
    console.log("Processing payment");
    this.booking.setState(new PaidState(this.booking));
  }
  
  cancel() {
    console.log("Cancelling confirmed booking");
    this.booking.setState(new CancelledState(this.booking));
  }
  
  checkIn() {
    console.log("Cannot check in until booking is paid");
  }
  
  checkOut() {
    console.log("Cannot check out of a booking that hasn't been checked in");
  }
  
  review() {
    console.log("Cannot review a booking that hasn't been completed");
  }
}

class PaidState extends BookingState {
  requestToBook() {
    console.log("Booking already confirmed and paid");
  }
  
  pay() {
    console.log("Booking already paid");
  }
  
  cancel() {
    console.log("Cancelling paid booking, processing refund");
    this.booking.setState(new CancelledState(this.booking));
  }
  
  checkIn() {
    console.log("Checking in to property");
    this.booking.setState(new CheckedInState(this.booking));
  }
  
  checkOut() {
    console.log("Cannot check out of a booking that hasn't been checked in");
  }
  
  review() {
    console.log("Cannot review a booking that hasn't been completed");
  }
}

class CheckedInState extends BookingState {
  requestToBook() {
    console.log("Already checked in to this booking");
  }
  
  pay() {
    console.log("Booking already paid");
  }
  
  cancel() {
    console.log("Cannot cancel a booking that's already in progress");
  }
  
  checkIn() {
    console.log("Already checked in");
  }
  
  checkOut() {
    console.log("Checking out of property");
    this.booking.setState(new CompletedState(this.booking));
  }
  
  review() {
    console.log("Cannot review a booking until after check-out");
  }
}

class CompletedState extends BookingState {
  requestToBook() {
    console.log("Booking already completed, create a new booking");
  }
  
  pay() {
    console.log("Booking already paid and completed");
  }
  
  cancel() {
    console.log("Cannot cancel a completed booking");
  }
  
  checkIn() {
    console.log("Booking already completed");
  }
  
  checkOut() {
    console.log("Already checked out");
  }
  
  review() {
    console.log("Adding review for completed stay");
    // Process review
  }
}

class CancelledState extends BookingState {
  requestToBook() {
    console.log("Booking was cancelled, please create a new booking");
  }
  
  pay() {
    console.log("Cannot pay for a cancelled booking");
  }
  
  cancel() {
    console.log("Booking already cancelled");
  }
  
  checkIn() {
    console.log("Cannot check in to a cancelled booking");
  }
  
  checkOut() {
    console.log("Cannot check out of a cancelled booking");
  }
  
  review() {
    console.log("Cannot review a cancelled booking");
  }
}

// Context
class Booking {
  constructor(propertyId, userId) {
    this.propertyId = propertyId;
    this.userId = userId;
    this.state = new PendingState(this);
  }
  
  setState(state) {
    this.state = state;
  }
  
  requestToBook() {
    this.state.requestToBook();
  }
  
  confirmBooking() {
    console.log("Host approved booking request");
    this.setState(new ConfirmedState(this));
  }
  
  pay() {
    this.state.pay();
  }
  
  cancel() {
    this.state.cancel();
  }
  
  checkIn() {
    this.state.checkIn();
  }
  
  checkOut() {
    this.state.checkOut();
  }
  
  review() {
    this.state.review();
  }
}

// Usage
const booking = new Booking("property123", "user456");

// Booking lifecycle
booking.requestToBook();
booking.confirmBooking();
booking.pay();
booking.checkIn();
booking.checkOut();
booking.review();

// Try operations in invalid states
const booking2 = new Booking("property789", "user012");
booking2.checkIn(); // Should not be allowed in Pending state
booking2.cancel();  // Should transition to Cancelled
booking2.pay();     // Should not be allowed in Cancelled state
```

**Pros**:
- Localizes state-specific behavior in separate classes
- Makes state transitions explicit
- Eliminates large conditional statements
- Allows for adding new states easily

**Cons**:
- Can lead to many small state classes
- If state transitions are frequent, the overhead might be significant

### Chain of Responsibility

**Description**: Passes a request along a chain of handlers. Upon receiving a request, each handler decides either to process the request or to pass it to the next handler in the chain.

**When to use it**:
- When more than one object may handle a request, and the handler isn't known a priori
- When you want to issue a request to one of several objects without specifying the receiver explicitly

**Real-world examples**:
- Middleware in web frameworks
- Event handling systems
- Approval workflows

**Example implementation**:
```javascript
// Handler interface
class BookingValidator {
  constructor() {
    this.nextValidator = null;
  }
  
  setNext(validator) {
    this.nextValidator = validator;
    return validator; // Return for convenient chaining
  }
  
  validate(booking) {
    if (this.nextValidator) {
      return this.nextValidator.validate(booking);
    }
    
    return true; // If no next validator, validation passes
  }
}

// Concrete handlers
class DateValidator extends BookingValidator {
  validate(booking) {
    console.log("Validating booking dates...");
    
    const today = new Date();
    const checkIn = new Date(booking.checkIn);
    const checkOut = new Date(booking.checkOut);
    
    if (checkIn < today) {
      console.log("Error: Check-in date cannot be in the past");
      return false;
    }
    
    if (checkOut <= checkIn) {
      console.log("Error: Check-out date must be after check-in date");
      return false;
    }
    
    console.log("Date validation passed");
    return super.validate(booking);
  }
}

class PropertyAvailabilityValidator extends BookingValidator {
  constructor(bookingService) {
    super();
    this.bookingService = bookingService;
  }
  
  validate(booking) {
    console.log("Checking property availability...");
    
    const isAvailable = this.bookingService.checkAvailability(
      booking.propertyId,
      booking.checkIn,
      booking.checkOut
    );
    
    if (!isAvailable) {
      console.log("Error: Property not available for the selected dates");
      return false;
    }
    
    console.log("Property availability validation passed");
    return super.validate(booking);
  }
}

class UserValidator extends BookingValidator {
  constructor(userService) {
    super();
    this.userService = userService;
  }
  
  validate(booking) {
    console.log("Validating user account...");
    
    const user = this.userService.getUser(booking.userId);
    
    if (!user) {
      console.log("Error: User not found");
      return false;
    }
    
    if (user.isBlocked) {
      console.log("Error: User is blocked from making bookings");
      return false;
    }
    
    console.log("User validation passed");
    return super.validate(booking);
  }
}

class PaymentValidator extends BookingValidator {
  validate(booking) {
    console.log("Validating payment information...");
    
    if (!booking.paymentMethod) {
      console.log("Error: No payment method provided");
      return false;
    }
    
    // Additional payment validation logic
    
    console.log("Payment validation passed");
    return super.validate(booking);
  }
}

// Client (uses the chain)
class BookingProcessor {
  constructor() {
    // Set up the chain
    const dateValidator = new DateValidator();
    const propertyValidator = new PropertyAvailabilityValidator(bookingService);
    const userValidator = new UserValidator(userService);
    const paymentValidator = new PaymentValidator();
    
    dateValidator
      .setNext(propertyValidator)
      .setNext(userValidator)
      .setNext(paymentValidator);
    
    this.validator = dateValidator;
  }
  
  processBooking(booking) {
    console.log("Processing booking request...");
    
    if (this.validator.validate(booking)) {
      console.log("Booking validation passed, creating booking");
      // Proceed with booking creation
      return true;
    } else {
      console.log("Booking validation failed");
      return false;
    }
  }
}

// Simulated services
const bookingService = {
  checkAvailability: (propertyId, checkIn, checkOut) => {
    // Simulate availability check
    return true;
  }
};

const userService = {
  getUser: (userId) => {
    // Simulate user lookup
    return { id: userId, isBlocked: false };
  }
};

// Usage
const booking = {
  propertyId: "property123",
  userId: "user456",
  checkIn: "2023-06-10",
  checkOut: "2023-06-15",
  paymentMethod: "credit_card"
};

const processor = new BookingProcessor();
processor.processBooking(booking);

// Test invalid booking
const invalidBooking = {
  propertyId: "property789",
  userId: "user012",
  checkIn: "2022-01-01", // Past date
  checkOut: "2023-06-15",
  paymentMethod: "credit_card"
};

processor.processBooking(invalidBooking);
```

**Pros**:
- Decouples the sender and receiver of a request
- Simplifies your object by only dealing with a single successor link
- Allows adding or removing responsibilities dynamically
- Each handler can focus on its specific validation or processing

**Cons**:
- Request can go unhandled if not carefully designed
- Can be hard to debug if the chain is long and complex
- May lead to duplicate code across handlers

### Mediator

**Description**: Defines an object that encapsulates how a set of objects interact. Promotes loose coupling by keeping objects from referring to each other explicitly.

**When to use it**:
- When communication between objects is complex but well-defined
- When reusing an object is difficult because it communicates with many other objects
- When a behavior that's distributed between several classes should be customizable without a lot of subclassing

**Real-world examples**:
- Air traffic control systems
- Chat rooms
- Ridesharing/reservations coordination

**Example implementation**:
```javascript
// Mediator interface
class BookingCoordinator {
  registerProperty(property) {}
  registerUser(user) {}
  bookProperty(userId, propertyId, dates) {}
  cancelBooking(bookingId) {}
  sendMessage(from, to, message) {}
}

// Concrete mediator
class AirbnbBookingCoordinator extends BookingCoordinator {
  constructor() {
    super();
    this.properties = {};
    this.users = {};
    this.bookings = {};
    this.nextBookingId = 1;
  }
  
  registerProperty(property) {
    this.properties[property.id] = property;
    property.setCoordinator(this);
  }
  
  registerUser(user) {
    this.users[user.id] = user;
    user.setCoordinator(this);
  }
  
  bookProperty(userId, propertyId, dates) {
    const user = this.users[userId];
    const property = this.properties[propertyId];
    
    if (!user || !property) {
      return { success: false, message: "User or property not found" };
    }
    
    if (property.isAvailable(dates)) {
      const bookingId = this.nextBookingId++;
      this.bookings[bookingId] = {
        id: bookingId,
        userId,
        propertyId,
        dates,
        status: "confirmed"
      };
      
      // Notify property of booking
      property.notifyBooked(bookingId, dates);
      
      // Notify user of confirmation
      user.notifyBookingConfirmed(bookingId, propertyId, dates);
      
      return { success: true, bookingId };
    } else {
      return { success: false, message: "Property not available for these dates" };
    }
  }
  
  cancelBooking(bookingId) {
    const booking = this.bookings[bookingId];
    
    if (!booking) {
      return { success: false, message: "Booking not found" };
    }
    
    // Update booking status
    booking.status = "cancelled";
    
    // Notify property of cancellation
    const property = this.properties[booking.propertyId];
    property.notifyCancelled(bookingId, booking.dates);
    
    // Notify user of cancellation
    const user = this.users[booking.userId];
    user.notifyBookingCancelled(bookingId);
    
    return { success: true };
  }
  
  sendMessage(fromId, toId, message) {
    const from = this.users[fromId] || this.properties[fromId];
    const to = this.users[toId] || this.properties[toId];
    
    if (!from || !to) {
      return { success: false, message: "Sender or recipient not found" };
    }
    
    to.receiveMessage(fromId, message);
    return { success: true };
  }
}

// Colleague classes
class User {
  constructor(id, name) {
    this.id = id;
    this.name = name;
    this.coordinator = null;
    this.bookings = [];
    this.messages = [];
  }
  
  setCoordinator(coordinator) {
    this.coordinator = coordinator;
  }
  
  bookProperty(propertyId, dates) {
    return this.coordinator.bookProperty(this.id, propertyId, dates);
  }
  
  cancelBooking(bookingId) {
    return this.coordinator.cancelBooking(bookingId);
  }
  
  sendMessage(recipientId, message) {
    return this.coordinator.sendMessage(this.id, recipientId, message);
  }
  
  notifyBookingConfirmed(bookingId, propertyId, dates) {
    console.log(`User ${this.name} notified: Booking #${bookingId} confirmed for property ${propertyId}`);
    this.bookings.push({ id: bookingId, propertyId, dates, status: "confirmed" });
  }
  
  notifyBookingCancelled(bookingId) {
    console.log(`User ${this.name} notified: Booking #${bookingId} cancelled`);
    const booking = this.bookings.find(b => b.id === bookingId);
    if (booking) {
      booking.status = "cancelled";
    }
  }
  
  receiveMessage(fromId, message) {
    console.log(`User ${this.name} received message from ${fromId}: ${message}`);
    this.messages.push({ from: fromId, text: message, timestamp: new Date() });
  }
}

class Property {
  constructor(id, name, hostId) {
    this.id = id;
    this.name = name;
    this.hostId = hostId;
    this.coordinator = null;
    this.bookings = [];
    this.messages = [];
    this.calendar = {}; // Date availability
  }
  
  setCoordinator(coordinator) {
    this.coordinator = coordinator;
  }
  
  isAvailable(dates) {
    // Check if property is available for these dates
    // Simplified implementation
    return !this.bookings.some(booking => 
      booking.status === "confirmed" && 
      datesOverlap(booking.dates, dates)
    );
  }
  
  notifyBooked(bookingId, dates) {
    console.log(`Property ${this.name} booked for dates: ${dates.checkIn} to ${dates.checkOut}`);
    this.bookings.push({ id: bookingId, dates, status: "confirmed" });
    
    // Notify host
    this.coordinator.sendMessage(
      this.id,
      this.hostId,
      `Your property "${this.name}" has been booked (Booking #${bookingId})`
    );
  }
  
  notifyCancelled(bookingId, dates) {
    console.log(`Property ${this.name} booking #${bookingId} cancelled`);
    const booking = this.bookings.find(b => b.id === bookingId);
    if (booking) {
      booking.status = "cancelled";
    }
    
    // Notify host
    this.coordinator.sendMessage(
      this.id,
      this.hostId,
      `A booking for your property "${this.name}" has been cancelled (Booking #${bookingId})`
    );
  }
  
  receiveMessage(fromId, message) {
    console.log(`Property ${this.name} received message from ${fromId}: ${message}`);
    this.messages.push({ from: fromId, text: message, timestamp: new Date() });
    
    // Forward to host
    this.coordinator.sendMessage(
      this.id,
      this.hostId,
      `Message for your property "${this.name}": ${message}`
    );
  }
}

// Utility function for date comparison
function datesOverlap(dates1, dates2) {
  const start1 = new Date(dates1.checkIn);
  const end1 = new Date(dates1.checkOut);
  const start2 = new Date(dates2.checkIn);
  const end2 = new Date(dates2.checkOut);
  
  return start1 < end2 && start2 < end1;
}

// Usage
const coordinator = new AirbnbBookingCoordinator();

const host = new User("host1", "Host User");
const guest = new User("guest1", "Guest User");

const property = new Property("prop1", "Beach House", "host1");

// Register with the coordinator
coordinator.registerUser(host);
coordinator.registerUser(guest);
coordinator.registerProperty(property);

// Guest books property
const bookingResult = guest.bookProperty("prop1", {
  checkIn: "2023-06-10",
  checkOut: "2023-06-15"
});

console.log("Booking result:", bookingResult);

// Guest messages property
guest.sendMessage("prop1", "Is there a grill available to use?");

// Guest cancels booking
if (bookingResult.success) {
  const cancellationResult = guest.cancelBooking(bookingResult.bookingId);
  console.log("Cancellation result:", cancellationResult);
}
```

**Pros**:
- Reduces coupling between components
- Centralizes communication between different parts of a system
- Makes it easier to modify how objects interact
- Abstracts how objects cooperate

**Cons**:
- Can become a "god object" with too many responsibilities
- Can become a performance bottleneck
- Can be difficult to test in isolation

### Memento

**Description**: Captures and externalizes an object's internal state without violating encapsulation, making it possible to restore the object to this state later.

**When to use it**:
- When you need to provide undo/redo functionality
- When you need to create snapshots of an object's state
- When direct access to an object's internal state would violate encapsulation

**Real-world examples**:
- Text editors with undo/redo
- Version control systems
- Save game states

**Example implementation**:
```javascript
// Memento class
class ListingStateMemento {
  constructor(state) {
    // Create a deep copy of the state
    this.state = JSON.parse(JSON.stringify(state));
  }
  
  getState() {
    return this.state;
  }
}

// Originator
class PropertyListing {
  constructor(id, title, description) {
    this.id = id;
    this.title = title;
    this.description = description;
    this.price = 0;
    this.features = [];
    this.images = [];
    this.availability = [];
  }
  
  setTitle(title) {
    this.title = title;
  }
  
  setDescription(description) {
    this.description = description;
  }
  
  setPrice(price) {
    this.price = price;
  }
  
  addFeature(feature) {
    this.features.push(feature);
  }
  
  addImage(image) {
    this.images.push(image);
  }
  
  setAvailability(availability) {
    this.availability = availability;
  }
  
  createMemento() {
    return new ListingStateMemento({
      id: this.id,
      title: this.title,
      description: this.description,
      price: this.price,
      features: this.features,
      images: this.images,
      availability: this.availability
    });
  }
  
  restoreFromMemento(memento) {
    const state = memento.getState();
    this.id = state.id;
    this.title = state.title;
    this.description = state.description;
    this.price = state.price;
    this.features = state.features;
    this.images = state.images;
    this.availability = state.availability;
  }
  
  getDetails() {
    return {
      id: this.id,
      title: this.title,
      description: this.description,
      price: this.price,
      features: this.features,
      images: this.images,
      availability: this.availability
    };
  }
}

// Caretaker
class ListingHistory {
  constructor() {
    this.history = [];
    this.currentIndex = -1;
  }
  
  saveState(listing) {
    // When saving a new state, truncate any forward history
    if (this.currentIndex < this.history.length - 1) {
      this.history = this.history.slice(0, this.currentIndex + 1);
    }
    
    this.history.push(listing.createMemento());
    this.currentIndex = this.history.length - 1;
  }
  
  undo(listing) {
    if (this.currentIndex > 0) {
      this.currentIndex--;
      listing.restoreFromMemento(this.history[this.currentIndex]);
      return true;
    }
    return false;
  }
  
  redo(listing) {
    if (this.currentIndex < this.history.length - 1) {
      this.currentIndex++;
      listing.restoreFromMemento(this.history[this.currentIndex]);
      return true;
    }
    return false;
  }
}

// Usage
const listing = new PropertyListing("prop1", "Beach House", "Beautiful beach house with ocean view");
const history = new ListingHistory();

// Save initial state
history.saveState(listing);

// Make some changes
listing.setPrice(150);
listing.addFeature("WiFi");
listing.addFeature("Kitchen");

// Save state after changes
history.saveState(listing);

console.log("Current listing state:", listing.getDetails());

// Make more changes
listing.setTitle("Luxury Beach House");
listing.setPrice(200);

// Save state after more changes
history.saveState(listing);

console.log("Current listing state:", listing.getDetails());

// Undo to previous state
history.undo(listing);
console.log("After undo:", listing.getDetails());

// Undo again to initial state
history.undo(listing);
console.log("After second undo:", listing.getDetails());

// Redo to go forward again
history.redo(listing);
console.log("After redo:", listing.getDetails());
```

**Pros**:
- Preserves encapsulation
- Simplifies the originator (the object being saved)
- Provides a clean way to implement undo/redo
- Can be used to restore objects to a previous state

**Cons**:
- Can be expensive in terms of memory if the state is large
- May be complex to implement for objects with many references
- Caretaker must carefully manage mementos

### Visitor

**Description**: Represents an operation to be performed on elements of an object structure. Visitor lets you define a new operation without changing the classes of the elements on which it operates.

**When to use it**:
- When you need to perform operations on all elements of a complex object structure
- When the classes in your object structure have differing interfaces
- When you need to add new operations without changing the classes of the elements

**Real-world examples**:
- Report generation from object structures
- XML/HTML document processing
- Compiler syntax trees

**Example implementation**:
```javascript
// Visitor interface
class PropertyVisitor {
  visitApartment(apartment) {}
  visitHouse(house) {}
  visitVilla(villa) {}
}

// Concrete visitors
class PricingVisitor extends PropertyVisitor {
  constructor() {
    super();
    this.totalPrice = 0;
  }
  
  visitApartment(apartment) {
    // Calculate apartment price based on size, location, etc.
    const price = apartment.squareMeters * 10 + (apartment.floor * 5);
    console.log(`Apartment pricing: $${price}`);
    this.totalPrice += price;
  }
  
  visitHouse(house) {
    // Calculate house price
    const price = house.squareMeters * 15 + (house.hasGarden ? 50 : 0);
    console.log(`House pricing: $${price}`);
    this.totalPrice += price;
  }
  
  visitVilla(villa) {
    // Calculate villa price
    const price = villa.squareMeters * 20 + (villa.hasPool ? 100 : 0) + (villa.hasGarden ? 50 : 0);
    console.log(`Villa pricing: $${price}`);
    this.totalPrice += price;
  }
  
  getTotalPrice() {
    return this.totalPrice;
  }
}

class CleaningVisitor extends PropertyVisitor {
  constructor() {
    super();
    this.totalTime = 0;
  }
  
  visitApartment(apartment) {
    // Calculate cleaning time in minutes
    const time = apartment.squareMeters * 0.5;
    console.log(`Apartment cleaning time: ${time} minutes`);
    this.totalTime += time;
  }
  
  visitHouse(house) {
    // Calculate cleaning time
    const time = house.squareMeters * 0.7 + (house.hasGarden ? 30 : 0);
    console.log(`House cleaning time: ${time} minutes`);
    this.totalTime += time;
  }
  
  visitVilla(villa) {
    // Calculate cleaning time
    const time = villa.squareMeters * 1 + (villa.hasPool ? 60 : 0) + (villa.hasGarden ? 30 : 0);
    console.log(`Villa cleaning time: ${time} minutes`);
    this.totalTime += time;
  }
  
  getTotalTime() {
    return this.totalTime;
  }
}

// Element interface
class Property {
  accept(visitor) {}
}

// Concrete elements
class Apartment extends Property {
  constructor(id, squareMeters, floor) {
    super();
    this.id = id;
    this.squareMeters = squareMeters;
    this.floor = floor;
  }
  
  accept(visitor) {
    visitor.visitApartment(this);
  }
}

class House extends Property {
  constructor(id, squareMeters, hasGarden) {
    super();
    this.id = id;
    this.squareMeters = squareMeters;
    this.hasGarden = hasGarden;
  }
  
  accept(visitor) {
    visitor.visitHouse(this);
  }
}

class Villa extends Property {
  constructor(id, squareMeters, hasPool, hasGarden) {
    super();
    this.id = id;
    this.squareMeters = squareMeters;
    this.hasPool = hasPool;
    this.hasGarden = hasGarden;
  }
  
  accept(visitor) {
    visitor.visitVilla(this);
  }
}

// Object structure
class PropertyPortfolio {
  constructor() {
    this.properties = [];
  }
  
  addProperty(property) {
    this.properties.push(property);
  }
  
  accept(visitor) {
    for (const property of this.properties) {
      property.accept(visitor);
    }
  }
}

// Usage
const portfolio = new PropertyPortfolio();

portfolio.addProperty(new Apartment("apt1", 80, 3));
portfolio.addProperty(new House("house1", 120, true));
portfolio.addProperty(new Villa("villa1", 200, true, true));
portfolio.addProperty(new Apartment("apt2", 60, 1));

// Calculate pricing
const pricingVisitor = new PricingVisitor();
portfolio.accept(pricingVisitor);
console.log(`Total pricing: $${pricingVisitor.getTotalPrice()}`);

// Calculate cleaning time
const cleaningVisitor = new CleaningVisitor();
portfolio.accept(cleaningVisitor);
console.log(`Total cleaning time: ${cleaningVisitor.getTotalTime()} minutes`);
```

**Pros**:
- Makes adding new operations easy
- Enables operations on complex object structures
- Gathers related operations and separates unrelated ones
- Allows accumulating state while traversing the object structure

**Cons**:
- Breaks encapsulation of the visited classes
- Requires updating all visitors when the element hierarchy changes
- Can be overkill for simple object structures

## Architectural Patterns

Architectural patterns address broader system-level concerns and provide a framework for organizing software components.

### Model-View-Controller (MVC)

**Description**: Separates an application into three main components: Model (data), View (user interface), and Controller (business logic).

**When to use it**:
- When you want to separate concerns in your application
- When you need multiple views for the same data
- When user interface and business rules change at different rates

**Real-world examples**:
- Web frameworks (Ruby on Rails, Django)
- GUI applications
- Mobile apps

**Example implementation**:
```javascript
// Model - Handles data and business logic
class PropertyModel {
  constructor() {
    this.properties = [];
    this.listeners = [];
  }
  
  addPropertyChangeListener(listener) {
    this.listeners.push(listener);
  }
  
  notifyListeners() {
    for (const listener of this.listeners) {
      listener.update(this);
    }
  }
  
  addProperty(property) {
    this.properties.push(property);
    this.notifyListeners();
  }
  
  removeProperty(propertyId) {
    this.properties = this.properties.filter(p => p.id !== propertyId);
    this.notifyListeners();
  }
  
  getProperty(propertyId) {
    return this.properties.find(p => p.id === propertyId);
  }
  
  getAllProperties() {
    return [...this.properties];
  }
  
  searchProperties(criteria) {
    // Filter properties based on search criteria
    return this.properties.filter(property => {
      let matches = true;
      
      if (criteria.location && !property.location.includes(criteria.location)) {
        matches = false;
      }
      
      if (criteria.minPrice && property.price < criteria.minPrice) {
        matches = false;
      }
      
      if (criteria.maxPrice && property.price > criteria.maxPrice) {
        matches = false;
      }
      
      return matches;
    });
  }
}

// View - Handles the visual representation
class PropertyListView {
  constructor(controller) {
    this.controller = controller;
    this.element = document.getElementById('property-list');
  }
  
  update(model) {
    // Clear the current view
    this.element.innerHTML = '';
    
    // Render all properties
    const properties = model.getAllProperties();
    for (const property of properties) {
      const propertyElement = document.createElement('div');
      propertyElement.className = 'property-card';
      
      propertyElement.innerHTML = `
        <h3>${property.title}</h3>
        <p>${property.description}</p>
        <p>$${property.price} per night</p>
        <button class="book-btn" data-id="${property.id}">Book Now</button>
      `;
      
      // Add event listener to the book button
      const bookBtn = propertyElement.querySelector('.book-btn');
      bookBtn.addEventListener('click', () => {
        this.controller.bookProperty(property.id);
      });
      
      this.element.appendChild(propertyElement);
    }
  }
  
  bindSearchForm() {
    const searchForm = document.getElementById('search-form');
    searchForm.addEventListener('submit', (event) => {
      event.preventDefault();
      
      const location = document.getElementById('location').value;
      const minPrice = document.getElementById('min-price').value;
      const maxPrice = document.getElementById('max-price').value;
      
      this.controller.searchProperties({
        location,
        minPrice: minPrice ? Number(minPrice) : null,
        maxPrice: maxPrice ? Number(maxPrice) : null
      });
    });
  }
}

// Controller - Handles user input and business operations
class PropertyController {
  constructor(model, view) {
    this.model = model;
    this.view = view;
    
    // Set up the model-view communication
    this.model.addPropertyChangeListener(this.view);
    
    // Bind UI events
    this.view.bindSearchForm();
    
    // Initial view update
    this.view.update(this.model);
  }
  
  addProperty(property) {
    this.model.addProperty(property);
  }
  
  removeProperty(propertyId) {
    this.model.removeProperty(propertyId);
  }
  
  bookProperty(propertyId) {
    const property = this.model.getProperty(propertyId);
    if (property) {
      // Logic to book the property
      console.log(`Booking property: ${property.title}`);
      // Could navigate to a booking page or open a modal
    }
  }
  
  searchProperties(criteria) {
    const results = this.model.searchProperties(criteria);
    // Could update a separate search results view
    console.log(`Found ${results.length} properties matching criteria`);
  }
}

// Usage (in a browser environment)
/*
const model = new PropertyModel();
const view = new PropertyListView();
const controller = new PropertyController(model, view);

// Add sample properties
controller.addProperty({
  id: 'p1',
  title: 'Cozy Apartment',
  description: 'Nice apartment in downtown',
  price: 100,
  location: 'New York'
});

controller.addProperty({
  id: 'p2',
  title: 'Beach House',
  description: 'Beautiful house with ocean view',
  price: 200,
  location: 'Miami'
});
*/
```

**Pros**:
- Separates concerns, making the application easier to maintain
- Allows multiple views of the same model
- Makes it easier to test components in isolation
- Reduces coupling between data, business logic, and presentation

**Cons**:
- Can introduce complexity for small applications
- Can lead to an excessive number of updates for complex views
- Controller can become bloated in large applications

### Model-View-ViewModel (MVVM)

**Description**: A variation of MVC focused on separating the UI development from the business logic and backend services.

**When to use it**:
- When working with modern UI frameworks like React, Vue, or Angular
- When you need data binding between the UI and the data model
- When you want designers to work on the UI without affecting business logic

**Real-world examples**:
- Modern web applications
- Mobile apps (especially with declarative UI frameworks)
- Desktop applications with complex UIs

**Example implementation** (using a hypothetical framework with data binding):
```javascript
// Model - Data and business logic
class PropertyListingModel {
  constructor() {
    this.properties = [];
    this.bookings = [];
  }
  
  async fetchProperties() {
    // In a real application, this would call an API
    this.properties = [
      {
        id: 'p1',
        title: 'Cozy Apartment',
        description: 'Nice apartment in downtown',
        price: 100,
        location: 'New York',
        imageUrl: 'apartment.jpg'
      },
      {
        id: 'p2',
        title: 'Beach House',
        description: 'Beautiful house with ocean view',
        price: 200,
        location: 'Miami',
        imageUrl: 'beach-house.jpg'
      }
    ];
    
    return this.properties;
  }
  
  async bookProperty(propertyId, dates) {
    // In a real application, this would call an API
    const booking = {
      id: `b${this.bookings.length + 1}`,
      propertyId,
      dates,
      status: 'confirmed'
    };
    
    this.bookings.push(booking);
    return booking;
  }
  
  async cancelBooking(bookingId) {
    const booking = this.bookings.find(b => b.id === bookingId);
    if (booking) {
      booking.status = 'cancelled';
      return true;
    }
    return false;
  }
}

// ViewModel - Connects the Model to the View
class PropertyListingViewModel {
  constructor(model) {
    this.model = model;
    
    // Observable properties for the view
    this.properties = [];
    this.filteredProperties = [];
    this.isLoading = false;
    this.error = null;
    
    // Form fields
    this.searchLocation = '';
    this.searchMinPrice = '';
    this.searchMaxPrice = '';
    
    // Selected property for booking
    this.selectedProperty = null;
    this.bookingDates = {
      checkIn: '',
      checkOut: ''
    };
    
    // Booking status
    this.isBooking = false;
    this.bookingSuccess = false;
    this.bookingError = null;
  }
  
  // Methods to handle user actions
  async loadProperties() {
    this.isLoading = true;
    this.error = null;
    
    try {
      this.properties = await this.model.fetchProperties();
      this.filteredProperties = [...this.properties];
    } catch (err) {
      this.error = 'Failed to load properties';
      console.error(err);
    } finally {
      this.isLoading = false;
    }
  }
  
  // Search functionality
  updateSearchLocation(location) {
    this.searchLocation = location;
    this.filterProperties();
  }
  
  updateSearchMinPrice(price) {
    this.searchMinPrice = price;
    this.filterProperties();
  }
  
  updateSearchMaxPrice(price) {
    this.searchMaxPrice = price;
    this.filterProperties();
  }
  
  filterProperties() {
    this.filteredProperties = this.properties.filter(property => {
      let matches = true;
      
      if (this.searchLocation && !property.location.toLowerCase().includes(this.searchLocation.toLowerCase())) {
        matches = false;
      }
      
      if (this.searchMinPrice && property.price < Number(this.searchMinPrice)) {
        matches = false;
      }
      
      if (this.searchMaxPrice && property.price > Number(this.searchMaxPrice)) {
        matches = false;
      }
      
      return matches;
    });
  }
  
  // Booking functionality
  selectProperty(propertyId) {
    this.selectedProperty = this.properties.find(p => p.id === propertyId);
    this.bookingDates = {
      checkIn: '',
      checkOut: ''
    };
    this.bookingSuccess = false;
    this.bookingError = null;
  }
  
  updateCheckInDate(date) {
    this.bookingDates.checkIn = date;
  }
  
  updateCheckOutDate(date) {
    this.bookingDates.checkOut = date;
  }
  
  async bookSelectedProperty() {
    if (!this.selectedProperty || !this.bookingDates.checkIn || !this.bookingDates.checkOut) {
      this.bookingError = 'Please select valid dates';
      return;
    }
    
    this.isBooking = true;
    this.bookingError = null;
    
    try {
      await this.model.bookProperty(this.selectedProperty.id, this.bookingDates);
      this.bookingSuccess = true;
    } catch (err) {
      this.bookingError = 'Failed to book property';
      console.error(err);
    } finally {
      this.isBooking = false;
    }
  }
}

// View implementation would depend on the framework
// Here's a pseudo-implementation for React:

/*
class PropertyListingView extends React.Component {
  constructor(props) {
    super(props);
    this.viewModel = new PropertyListingViewModel(new PropertyListingModel());
  }
  
  componentDidMount() {
    this.viewModel.loadProperties();
  }
  
  render() {
    const vm = this.viewModel;
    
    if (vm.isLoading) {
      return <div>Loading properties...</div>;
    }
    
    if (vm.error) {
      return <div>Error: {vm.error}</div>;
    }
    
    return (
      <div>
        <div className="search-form">
          <input 
            type="text" 
            placeholder="Location" 
            value={vm.searchLocation}
            onChange={e => vm.updateSearchLocation(e.target.value)}
          />
          <input 
            type="number" 
            placeholder="Min Price" 
            value={vm.searchMinPrice}
            onChange={e => vm.updateSearchMinPrice(e.target.value)}
          />
          <input 
            type="number" 
            placeholder="Max Price" 
            value={vm.searchMaxPrice}
            onChange={e => vm.updateSearchMaxPrice(e.target.value)}
          />
        </div>
        
        <div className="property-list">
          {vm.filteredProperties.map(property => (
            <div key={property.id} className="property-card">
              <img src={property.imageUrl} alt={property.title} />
              <h3>{property.title}</h3>
              <p>{property.description}</p>
              <p>${property.price} per night</p>
              <button onClick={() => vm.selectProperty(property.id)}>
                Book Now
              </button>
            </div>
          ))}
        </div>
        
        {vm.selectedProperty && (
          <div className="booking-modal">
            <h2>Book {vm.selectedProperty.title}</h2>
            <div>
              <label>Check-in Date:</label>
              <input 
                type="date" 
                value={vm.bookingDates.checkIn}
                onChange={e => vm.updateCheckInDate(e.target.value)}
              />
            </div>
            <div>
              <label>Check-out Date:</label>
              <input 
                type="date" 
                value={vm.bookingDates.checkOut}
                onChange={e => vm.updateCheckOutDate(e.target.value)}
              />
            </div>
            
            {vm.bookingError && <div className="error">{vm.bookingError}</div>}
            {vm.bookingSuccess && <div className="success">Booking confirmed!</div>}
            
            <button 
              onClick={() => vm.bookSelectedProperty()}
              disabled={vm.isBooking}
            >
              {vm.isBooking ? 'Processing...' : 'Confirm Booking'}
            </button>
          </div>
        )}
      </div>
    );
  }
}
*/
```

**Pros**:
- Provides data binding between the View and ViewModel
- Simplifies UI development with reactive interfaces
- Makes UI more testable through the ViewModel
- Separates UI design from business logic

**Cons**:
- Can be overkill for simple applications
- Requires understanding of data binding concepts
- May lead to complex ViewModels for very interactive UIs

### Microservices

**Description**: An architectural style that structures an application as a collection of loosely coupled services, each implementing a specific business capability.

**When to use it**:
- When building large, complex applications that need to scale
- When different parts of the application evolve at different rates
- When you need to deploy different components independently

**Real-world examples**:
- Netflix's streaming platform
- Amazon's e-commerce platform
- Modern cloud-native applications

**Example implementation** (conceptual):
```javascript
// This is a conceptual representation of microservices for an AirBnB-like platform

// User Service
class UserService {
  constructor() {
    this.port = 3001;
    this.database = "users_db";
  }
  
  start() {
    console.log(`Starting User Service on port ${this.port}`);
    // Setup routes, database connection, etc.
    
    // Example endpoints:
    // GET /users/:id - Get user profile
    // POST /users - Create user
    // PUT /users/:id - Update user
    // POST /users/login - Authenticate user
  }
  
  createUser(userData) {
    // Validate and create user
    console.log(`Creating user: ${userData.email}`);
    return { id: "user123", ...userData };
  }
  
  getUser(userId) {
    // Fetch user from database
    console.log(`Fetching user: ${userId}`);
    return { id: userId, name: "John Doe", email: "john@example.com" };
  }
  
  updateUser(userId, userData) {
    // Update user in database
    console.log(`Updating user: ${userId}`);
    return { id: userId, ...userData };
  }
}

// Property Service
class PropertyService {
  constructor() {
    this.port = 3002;
    this.database = "properties_db";
  }
  
  start() {
    console.log(`Starting Property Service on port ${this.port}`);
    // Setup routes, database connection, etc.
    
    // Example endpoints:
    // GET /properties - List properties
    // GET /properties/:id - Get property details
    // POST /properties - Create property
    // PUT /properties/:id - Update property
  }
  
  listProperties(filters) {
    // Fetch properties with filters
    console.log(`Listing properties with filters: ${JSON.stringify(filters)}`);
    return [
      { id: "prop1", title: "Beach House", location: "Miami", price: 200 },
      { id: "prop2", title: "City Apartment", location: "New York", price: 150 }
    ];
  }
  
  getProperty(propertyId) {
    // Fetch property details
    console.log(`Fetching property: ${propertyId}`);
    return { 
      id: propertyId, 
      title: "Beach House", 
      description: "Beautiful house with ocean view",
      location: "Miami", 
      price: 200,
      amenities: ["WiFi", "Kitchen", "Pool"]
    };
  }
  
  createProperty(propertyData) {
    // Create new property listing
    console.log(`Creating property: ${propertyData.title}`);
    return { id: "prop123", ...propertyData };
  }
  
  updateProperty(propertyId, propertyData) {
    // Update property in database
    console.log(`Updating property: ${propertyId}`);
    return { id: propertyId, ...propertyData };
  }
}

// Booking Service
class BookingService {
  constructor() {
    this.port = 3003;
    this.database = "bookings_db";
  }
  
  start() {
    console.log(`Starting Booking Service on port ${this.port}`);
    // Setup routes, database connection, etc.
    
    // Example endpoints:
    // POST /bookings - Create booking
    // GET /bookings/:id - Get booking details
    // GET /bookings/user/:userId - Get user's bookings
    // PUT /bookings/:id/cancel - Cancel booking
  }
  
  createBooking(bookingData) {
    // Create a new booking
    console.log(`Creating booking for property: ${bookingData.propertyId}`);
    
    // In a real implementation, this would:
    // 1. Check property availability via PropertyService
    // 2. Validate user via UserService
    // 3. Create the booking record
    // 4. Initiate payment via PaymentService
    
    return { 
      id: "booking123", 
      status: "confirmed",
      ...bookingData
    };
  }
  
  getBooking(bookingId) {
    // Fetch booking details
    console.log(`Fetching booking: ${bookingId}`);
    return { 
      id: bookingId,
      propertyId: "prop1",
      userId: "user1",
      checkIn: "2023-06-10",
      checkOut: "2023-06-15",
      status: "confirmed"
    };
  }
  
  getUserBookings(userId) {
    // Fetch all bookings for a user
    console.log(`Fetching bookings for user: ${userId}`);
    return [
      { 
        id: "booking1",
        propertyId: "prop1",
        userId: userId,
        checkIn: "2023-06-10",
        checkOut: "2023-06-15",
        status: "confirmed"
      },
      { 
        id: "booking2",
        propertyId: "prop2",
        userId: userId,
        checkIn: "2023-07-05",
        checkOut: "2023-07-10",
        status: "pending"
      }
    ];
  }
  
  cancelBooking(bookingId) {
    // Cancel a booking
    console.log(`Cancelling booking: ${bookingId}`);
    return { 
      id: bookingId,
      status: "cancelled"
    };
  }
}

// Payment Service
class PaymentService {
  constructor() {
    this.port = 3004;
    this.database = "payments_db";
  }
  
  start() {
    console.log(`Starting Payment Service on port ${this.port}`);
    // Setup routes, database connection, etc.
    
    // Example endpoints:
    // POST /payments - Process payment
    // GET /payments/:id - Get payment details
    // POST /payments/:id/refund - Process refund
  }
  
  processPayment(paymentData) {
    // Process a payment
    console.log(`Processing payment of ${paymentData.amount}`);
    
    // In a real implementation, this would:
    // 1. Validate payment details
    // 2. Call payment gateway API
    // 3. Record payment result
    
    return { 
      id: "payment123", 
      status: "completed",
      timestamp: new Date().toISOString(),
      ...paymentData
    };
  }
  
  getPayment(paymentId) {
    // Fetch payment details
    console.log(`Fetching payment: ${paymentId}`);
    return { 
      id: paymentId,
      bookingId: "booking1",
      amount: 500,
      status: "completed",
      timestamp: "2023-05-15T14:30:45Z"
    };
  }
  
  processRefund(paymentId, refundData) {
    // Process a refund
    console.log(`Processing refund for payment: ${paymentId}`);
    
    return { 
      id: "refund123",
      paymentId: paymentId,
      amount: refundData.amount,
      status: "completed",
      timestamp: new Date().toISOString()
    };
  }
}

// Notification Service
class NotificationService {
  constructor() {
    this.port = 3005;
  }
  
  start() {
    console.log(`Starting Notification Service on port ${this.port}`);
    // Setup routes, message queue, etc.
    
    // Example endpoints:
    // POST /notifications/email - Send email
    // POST /notifications/sms - Send SMS
    // POST /notifications/push - Send push notification
  }
  
  sendEmail(emailData) {
    // Send email notification
    console.log(`Sending email to: ${emailData.recipient}`);
    console.log(`Subject: ${emailData.subject}`);
    
    return { 
      id: "notification123", 
      type: "email",
      status: "sent",
      timestamp: new Date().toISOString()
    };
  }
  
  sendSMS(smsData) {
    // Send SMS notification
    console.log(`Sending SMS to: ${smsData.phoneNumber}`);
    
    return { 
      id: "notification124", 
      type: "sms",
      status: "sent",
      timestamp: new Date().toISOString()
    };
  }
  
  sendPush(pushData) {
    // Send push notification
    console.log(`Sending push notification to device: ${pushData.deviceId}`);
    
    return { 
      id: "notification125", 
      type: "push",
      status: "sent",
      timestamp: new Date().toISOString()
    };
  }
}

// API Gateway
class ApiGateway {
  constructor(services) {
    this.port = 3000;
    this.services = services;
  }
  
  start() {
    console.log(`Starting API Gateway on port ${this.port}`);
    // Setup routes, authentication, etc.
    
    // The gateway would route requests to appropriate services
    // For example:
    // GET /api/users/:id -> UserService
    // GET /api/properties -> PropertyService
    // POST /api/bookings -> BookingService
  }
  
  handleRequest(path, method, data) {
    console.log(`API Gateway handling ${method} request to ${path}`);
    
    // Route the request to the appropriate service
    if (path.startsWith('/api/users')) {
      return this.services.userService.handleRequest(path, method, data);
    } else if (path.startsWith('/api/properties')) {
      return this.services.propertyService.handleRequest(path, method, data);
    } else if (path.startsWith('/api/bookings')) {
      return this.services.bookingService.handleRequest(path, method, data);
    } else if (path.startsWith('/api/payments')) {
      return this.services.paymentService.handleRequest(path, method, data);
    } else {
      return { error: "Route not found" };
    }
  }
  
  // In a real implementation, the gateway would also handle:
  // - Authentication and authorization
  // - Request/response transformation
  // - Rate limiting
  // - Logging and monitoring
  // - Error handling
}

// Usage (conceptual)
const services = {
  userService: new UserService(),
  propertyService: new PropertyService(),
  bookingService: new BookingService(),
  paymentService: new PaymentService(),
  notificationService: new NotificationService()
};

// Start all services
for (const service of Object.values(services)) {
  service.start();
}

// Start the API Gateway
const apiGateway = new ApiGateway(services);
apiGateway.start();

// Example client request flow:
// 1. Client requests to create a booking
const bookingRequest = {
  propertyId: "prop1",
  userId: "user1",
  checkIn: "2023-06-10",
  checkOut: "2023-06-15"
};

// 2. API Gateway routes to BookingService
const booking = services.bookingService.createBooking(bookingRequest);

// 3. BookingService initiates payment via PaymentService
const paymentRequest = {
  bookingId: booking.id,
  userId: booking.userId,
  amount: 500,
  paymentMethod: {
    type: "credit_card",
    cardNumber: "xxxx-xxxx-xxxx-1234",
    expiryDate: "12/25"
  }
};
const payment = services.paymentService.processPayment(paymentRequest);

// 4. Once payment is complete, BookingService confirms the booking
// and NotificationService sends confirmation
const emailData = {
  recipient: "user@example.com",
  subject: "Booking Confirmation",
  template: "booking-confirmation",
  data: {
    bookingId: booking.id,
    propertyTitle: "Beach House",
    checkIn: booking.checkIn,
    checkOut: booking.checkOut
  }
};
services.notificationService.sendEmail(emailData);
```

**Pros**:
- Enables independent development and deployment of services
- Allows different services to use different technologies
- Improves fault isolation and resilience
- Enables better scaling of specific components
- Facilitates continuous delivery and deployment

**Cons**:
- Increases complexity of the overall system
- Requires sophisticated deployment and monitoring infrastructure
- Distributed systems bring their own challenges (network latency, data consistency)
- May require significant initial investment

### Repository Pattern

**Description**: Mediates between the domain and data mapping layers, acting like an in-memory collection of domain objects.

**When to use it**:
- When you want to centralize data access logic
- When you need to isolate your domain model from your data access mechanism
- When you need to test your application without accessing the database

**Real-world examples**:
- Data access layers in enterprise applications
- ORM frameworks
- Database abstraction layers

**Example implementation**:
```javascript
// Entity class
class Property {
  constructor(id, title, description, price, location) {
    this.id = id;
    this.title = title;
    this.description = description;
    this.price = price;
    this.location = location;
    this.amenities = [];
    this.images = [];
    this.hostId = null;
  }
  
  addAmenity(amenity) {
    this.amenities.push(amenity);
  }
  
  addImage(imageUrl) {
    this.images.push(imageUrl);
  }
  
  setHost(hostId) {
    this.hostId = hostId;
  }
}

// Repository interface
class PropertyRepository {
  findById(id) {}
  findAll() {}
  findByLocation(location) {}
  findByPriceRange(minPrice, maxPrice) {}
  save(property) {}
  update(property) {}
  delete(id) {}
}

// Concrete repository implementation for MongoDB
class MongoPropertyRepository extends PropertyRepository {
  constructor(connectionString) {
    super();
    this.connectionString = connectionString;
    // In a real implementation, would connect to MongoDB
    console.log(`Connecting to MongoDB: ${connectionString}`);
    
    // Mock database for this example
    this.properties = new Map();
  }
  
  async findById(id) {
    console.log(`Finding property with ID: ${id}`);
    
    // In a real implementation, would query MongoDB
    // const property = await this.collection.findOne({ _id: id });
    
    // Mock implementation
    const property = this.properties.get(id);
    
    if (!property) {
      return null;
    }
    
    return this._mapToEntity(property);
  }
  
  async findAll() {
    console.log(`Finding all properties`);
    
    // In a real implementation, would query MongoDB
    // const properties = await this.collection.find({}).toArray();
    
    // Mock implementation
    const properties = Array.from(this.properties.values());
    
    return properties.map(p => this._mapToEntity(p));
  }
  
  async findByLocation(location) {
    console.log(`Finding properties in location: ${location}`);
    
    // In a real implementation, would query MongoDB
    // const properties = await this.collection.find({ location: { $regex: location, $options: 'i' } }).toArray();
    
    // Mock implementation
    const properties = Array.from(this.properties.values())
      .filter(p => p.location.toLowerCase().includes(location.toLowerCase()));
    
    return properties.map(p => this._mapToEntity(p));
  }
  
  async findByPriceRange(minPrice, maxPrice) {
    console.log(`Finding properties with price between ${minPrice} and ${maxPrice}`);
    
    // In a real implementation, would query MongoDB
    /* const properties = await this.collection.find({
      price: { $gte: minPrice, $lte: maxPrice }
    }).toArray(); */
    
    // Mock implementation
    const properties = Array.from(this.properties.values())
      .filter(p => p.price >= minPrice && p.price <= maxPrice);
    
    return properties.map(p => this._mapToEntity(p));
  }
  
  async save(property) {
    console.log(`Saving property: ${property.title}`);
    
    // In a real implementation, would insert into MongoDB
    // const result = await this.collection.insertOne(this._mapToDocument(property));
    // property.id = result.insertedId;
    
    // Mock implementation
    if (!property.id) {
      property.id = `prop_${Date.now()}`;
    }
    
    this.properties.set(property.id, this._mapToDocument(property));
    
    return property;
  }
  
  async update(property) {
    console.log(`Updating property: ${property.id}`);
    
    // In a real implementation, would update in MongoDB
    /* await this.collection.updateOne(
      { _id: property.id },
      { $set: this._mapToDocument(property) }
    ); */
    
    // Mock implementation
    if (!this.properties.has(property.id)) {
      throw new Error(`Property with ID ${property.id} not found`);
    }
    
    this.properties.set(property.id, this._mapToDocument(property));
    
    return property;
  }
  
  async delete(id) {
    console.log(`Deleting property: ${id}`);
    
    // In a real implementation, would delete from MongoDB
    // await this.collection.deleteOne({ _id: id });
    
    // Mock implementation
    return this.properties.delete(id);
  }
  
  // Helper methods to map between entity and database document
  _mapToEntity(document) {
    const property = new Property(
      document.id,
      document.title,
      document.description,
      document.price,
      document.location
    );
    
    property.amenities = [...document.amenities];
    property.images = [...document.images];
    property.hostId = document.hostId;
    
    return property;
  }
  
  _mapToDocument(entity) {
    return {
      id: entity.id,
      title: entity.title,
      description: entity.description,
      price: entity.price,
      location: entity.location,
      amenities: [...entity.amenities],
      images: [...entity.images],
      hostId: entity.hostId
    };
  }
}

// Alternative repository implementation for SQL database
class SQLPropertyRepository extends PropertyRepository {
  constructor(connectionString) {
    super();
    this.connectionString = connectionString;
    // In a real implementation, would connect to SQL database
    console.log(`Connecting to SQL database: ${connectionString}`);
    
    // Mock database for this example
    this.properties = new Map();
  }
  
  // Implementations of all methods similar to MongoPropertyRepository
  // but using SQL queries instead of MongoDB operations
  
  async findById(id) {
    console.log(`Finding property with ID: ${id} using SQL`);
    // In a real implementation: SELECT * FROM properties WHERE id = ?
    
    // Mock implementation
    const property = this.properties.get(id);
    
    if (!property) {
      return null;
    }
    
    return this._mapToEntity(property);
  }
  
  // Other methods would be implemented similarly
}

// Service class that uses the repository
class PropertyService {
  constructor(propertyRepository) {
    this.propertyRepository = propertyRepository;
  }
  
  async getProperty(id) {
    return this.propertyRepository.findById(id);
  }
  
  async searchProperties(criteria) {
    if (criteria.location) {
      return this.propertyRepository.findByLocation(criteria.location);
    } else if (criteria.minPrice && criteria.maxPrice) {
      return this.propertyRepository.findByPriceRange(criteria.minPrice, criteria.maxPrice);
    } else {
      return this.propertyRepository.findAll();
    }
  }
  
  async createProperty(propertyData) {
    const property = new Property(
      null,
      propertyData.title,
      propertyData.description,
      propertyData.price,
      propertyData.location
    );
    
    if (propertyData.amenities) {
      for (const amenity of propertyData.amenities) {
        property.addAmenity(amenity);
      }
    }
    
    if (propertyData.images) {
      for (const image of propertyData.images) {
        property.addImage(image);
      }
    }
    
    if (propertyData.hostId) {
      property.setHost(propertyData.hostId);
    }
    
    return this.propertyRepository.save(property);
  }
  
  async updateProperty(id, propertyData) {
    const property = await this.propertyRepository.findById(id);
    
    if (!property) {
      throw new Error(`Property with ID ${id} not found`);
    }
    
    if (propertyData.title) property.title = propertyData.title;
    if (propertyData.description) property.description = propertyData.description;
    if (propertyData.price) property.price = propertyData.price;
    if (propertyData.location) property.location = propertyData.location;
    
    if (propertyData.amenities) {
      property.amenities = [...propertyData.amenities];
    }
    
    if (propertyData.images) {
      property.images = [...propertyData.images];
    }
    
    if (propertyData.hostId) {
      property.hostId = propertyData.hostId;
    }
    
    return this.propertyRepository.update(property);
  }
  
  async deleteProperty(id) {
    return this.propertyRepository.delete(id);
  }
}

// Usage
async function demo() {
  // Create repository (could be either implementation)
  const propertyRepo = new MongoPropertyRepository("mongodb://localhost:27017/airbnb");
  
  // Create service with repository
  const propertyService = new PropertyService(propertyRepo);
  
  // Use the service
  const newProperty = await propertyService.createProperty({
    title: "Cozy Apartment",
    description: "Nice apartment in downtown",
    price: 100,
    location: "New York",
    amenities: ["WiFi", "Kitchen"],
    images: ["apartment1.jpg", "apartment2.jpg"],
    hostId: "user123"
  });
  
  console.log("Created property:", newProperty);
  
  // Search properties
  const propertiesInNY = await propertyService.searchProperties({
    location: "New York"
  });
  
  console.log("Properties in New York:", propertiesInNY);
  
  // Update property
  const updatedProperty = await propertyService.updateProperty(newProperty.id, {
    price: 120,
    amenities: ["WiFi", "Kitchen", "Air Conditioning"]
  });
  
  console.log("Updated property:", updatedProperty);
}

// demo();
```

**Pros**:
- Centralizes data access logic
- Provides a clean API for the rest of the application
- Makes the application more testable (by mocking the repository)
- Allows for easy switching of data sources

**Cons**:
- Adds an extra layer of abstraction
- May involve writing repetitive code for basic CRUD operations
- Can hide performance issues with inefficient queries

### Dependency Injection

**Description**: A technique where objects receive their dependencies from an external source rather than creating them internally.

**When to use it**:
- When you want to reduce coupling between components
- When you need to test components in isolation
- When your application needs to work with different implementations of interfaces

**Real-world examples**:
- Modern web frameworks (Angular, Spring, .NET Core)
- Enterprise applications
- Testable applications

**Example implementation**:
```javascript
// Interface (conceptual in JavaScript)
class Logger {
  log(message, level) {}
}

// Concrete implementations
class ConsoleLogger extends Logger {
  log(message, level = 'info') {
    console.log(`[${level.toUpperCase()}] ${message}`);
  }
}

class FileLogger extends Logger {
  constructor(filePath) {
    super();
    this.filePath = filePath;
  }
  
  log(message, level = 'info') {
    const logMessage = `[${new Date().toISOString()}] [${level.toUpperCase()}] ${message}`;
    // In a real implementation, would write to a file
    console.log(`Writing to ${this.filePath}: ${logMessage}`);
  }
}

// Service that depends on a logger
class BookingService {
  constructor(logger, paymentProcessor) {
    this.logger = logger;
    this.paymentProcessor = paymentProcessor;
  }
  
  createBooking(propertyId, userId, dates) {
    this.logger.log(`Creating booking for property ${propertyId}`, 'info');
    
    // Business logic for creating a booking
    const booking = {
      id: `booking-${Date.now()}`,
      propertyId,
      userId,
      dates,
      status: 'pending'
    };
    
    this.logger.log(`Booking created with ID ${booking.id}`, 'info');
    
    return booking;
  }
  
  confirmBooking(bookingId, paymentDetails) {
    this.logger.log(`Confirming booking ${bookingId}`, 'info');
    
    try {
      // Process payment
      const paymentResult = this.paymentProcessor.processPayment(paymentDetails);
      
      if (paymentResult.success) {
        this.logger.log(`Payment processed successfully for booking ${bookingId}`, 'info');
        
        // Update booking status
        const booking = { id: bookingId, status: 'confirmed' };
        return booking;
      } else {
        this.logger.log(`Payment failed for booking ${bookingId}: ${paymentResult.error}`, 'error');
        throw new Error(`Payment failed: ${paymentResult.error}`);
      }
    } catch (error) {
      this.logger.log(`Error confirming booking ${bookingId}: ${error.message}`, 'error');
      throw error;
    }
  }
}

// Another service with dependencies
class PaymentProcessor {
  constructor(logger, paymentGateway) {
    this.logger = logger;
    this.paymentGateway = paymentGateway;
  }
  
  processPayment(paymentDetails) {
    this.logger.log(`Processing payment of ${paymentDetails.amount}`, 'info');
    
    try {
      // Call payment gateway
      const result = this.paymentGateway.charge(paymentDetails);
      
      if (result.success) {
        this.logger.log('Payment successful', 'info');
      } else {
        this.logger.log(`Payment failed: ${result.error}`, 'error');
      }
      
      return result;
    } catch (error) {
      this.logger.log(`Payment error: ${error.message}`, 'error');
      return { success: false, error: error.message };
    }
  }
}

// Payment gateway interface
class PaymentGateway {
  charge(paymentDetails) {}
}

// Concrete payment gateway
class StripeGateway extends PaymentGateway {
  constructor(apiKey) {
    super();
    this.apiKey = apiKey;
  }
  
  charge(paymentDetails) {
    console.log(`Charging ${paymentDetails.amount} via Stripe`);
    // In a real implementation, would call Stripe API
    
    // Simulate successful payment
    return { success: true, transactionId: `stripe-${Date.now()}` };
  }
}

// Simple dependency injection container
class Container {
  constructor() {
    this.services = new Map();
    this.factories = new Map();
  }
  
  register(name, instance) {
    this.services.set(name, instance);
  }
  
  registerFactory(name, factory) {
    this.factories.set(name, factory);
  }
  
  resolve(name) {
    if (this.services.has(name)) {
      return this.services.get(name);
    }
    
    if (this.factories.has(name)) {
      const factory = this.factories.get(name);
      const instance = factory(this);
      this.services.set(name, instance);
      return instance;
    }
    
    throw new Error(`Service ${name} not registered`);
  }
}

// Usage
function setupContainer() {
  const container = new Container();
  
  // Register logger
  container.register('logger', new ConsoleLogger());
  
  // Register payment gateway
  container.register('paymentGateway', new StripeGateway('sk_test_123456'));
  
  // Register payment processor with dependencies
  container.registerFactory('paymentProcessor', (container) => {
    const logger = container.resolve('logger');
    const paymentGateway = container.resolve('paymentGateway');
    return new PaymentProcessor(logger, paymentGateway);
  });
  
  // Register booking service with dependencies
  container.registerFactory('bookingService', (container) => {
    const logger = container.resolve('logger');
    const paymentProcessor = container.resolve('paymentProcessor');
    return new BookingService(logger, paymentProcessor);
  });
  
  return container;
}

// Application usage
function bookProperty() {
  const container = setupContainer();
  
  // Resolve the booking service with all its dependencies
  const bookingService = container.resolve('bookingService');
  
  // Use the service
  const booking = bookingService.createBooking('property123', 'user456', {
    checkIn: '2023-06-10',
    checkOut: '2023-06-15'
  });
  
  // Confirm the booking with payment
  const confirmedBooking = bookingService.confirmBooking(booking.id, {
    amount: 500,
    cardNumber: '4242424242424242',
    expiryDate: '12/25',
    cvv: '123'
  });
  
  console.log('Booking confirmed:', confirmedBooking);
}

// bookProperty();

// For testing, we can easily swap implementations
function setupTestContainer() {
  const container = new Container();
  
  // Mock logger for testing
  container.register('logger', {
    log: (message, level) => { /* Do nothing */ }
  });
  
  // Mock payment gateway that always succeeds
  container.register('paymentGateway', {
    charge: (paymentDetails) => ({ success: true, transactionId: 'test-123' })
  });
  
  // Register services with mocked dependencies
  container.registerFactory('paymentProcessor', (container) => {
    const logger = container.resolve('logger');
    const paymentGateway = container.resolve('paymentGateway');
    return new PaymentProcessor(logger, paymentGateway);
  });
  
  container.registerFactory('bookingService', (container) => {
    const logger = container.resolve('logger');
    const paymentProcessor = container.resolve('paymentProcessor');
    return new BookingService(logger, paymentProcessor);
  });
  
  return container;
}

// Test function
function testBookingService() {
  const container = setupTestContainer();
  const bookingService = container.resolve('bookingService');
  
  // Test booking creation
  const booking = bookingService.createBooking('test-property', 'test-user', {
    checkIn: '2023-06-10',
    checkOut: '2023-06-15'
  });
  
  console.assert(booking.status === 'pending', 'Booking should have pending status');
  
  // Test booking confirmation
  const confirmedBooking = bookingService.confirmBooking(booking.id, {
    amount: 500,
    cardNumber: 'test'
  });
  
  console.assert(confirmedBooking.status === 'confirmed', 'Booking should be confirmed');
  
  console.log('All tests passed!');
}

// testBookingService();
```

**Pros**:
- Reduces coupling between components
- Makes testing easier through mocking
- Enables swapping implementations easily
- Supports the Single Responsibility Principle

**Cons**:
- Can increase complexity for small applications
- May require a dependency injection framework for large applications
- Can make code harder to follow if overused

## When to Use Design Patterns

Design patterns are powerful tools in a developer's toolkit, but they should be used judiciously. Here are some guidelines for when to use (and when not to use) design patterns:

### Good Times to Use Design Patterns

1. **When solving a common problem**: Design patterns address recurring problems. If you're facing a problem that's well-known and has established solutions, a design pattern may be appropriate.

2. **For communication**: Design patterns provide a common vocabulary for developers. Using them makes it easier to communicate your design to other team members.

3. **When refactoring**: If your code has become complex and difficult to maintain, introducing appropriate design patterns during refactoring can improve its structure.

4. **For future flexibility**: When you anticipate that certain aspects of your system may change, design patterns can help build in the flexibility to accommodate those changes.

5. **When building a framework**: Design patterns are especially valuable when creating frameworks or libraries that will be used by other developers.

### When to Avoid Design Patterns

1. **For simple problems**: If a straightforward solution exists, using a design pattern might unnecessarily complicate the code.

2. **When team members are unfamiliar with patterns**: If your team doesn't understand design patterns, introducing them without proper education can lead to confusion.

3. **When it's premature optimization**: Applying complex patterns before they're needed can lead to over-engineering and unnecessary complexity.

4. **When patterns don't fit the problem**: Forcing a pattern to fit a problem it wasn't designed for can result in awkward and hard-to-maintain code.

5. **When performance is critical**: Some patterns introduce additional layers of abstraction, which might impact performance in critical sections of your code.

## Alternatives to Design Patterns

While design patterns are valuable tools, they aren't the only approach to software design. Here are some alternatives:

### 1. SOLID Principles

The SOLID principles are a set of five design principles for writing maintainable and scalable software:

- **Single Responsibility Principle**: A class should have only one reason to change.
- **Open/Closed Principle**: Software entities should be open for extension but closed for modification.
- **Liskov Substitution Principle**: Objects of a superclass should be replaceable with objects of a subclass without affecting correctness.
- **Interface Segregation Principle**: Many client-specific interfaces are better than one general-purpose interface.
- **Dependency Inversion Principle**: Depend on abstractions, not concretions.

These principles can guide your design without necessarily using specific patterns.

### 2. Functional Programming

Functional programming emphasizes immutable data, pure functions, and function composition rather than object-oriented patterns:

```javascript
// Instead of using the Strategy pattern with classes
// Use higher-order functions
function calculateRegularPrice(basePricePerNight, nights) {
  return basePricePerNight * nights;
}

function calculateWeekendPrice(basePricePerNight, nights) {
  return basePricePerNight * nights * 1.5;
}

function calculateSeasonalPrice(basePricePerNight, nights) {
  const season = getCurrentSeason();
  const multipliers = {
    summer: 1.8,
    winter: 1.2,
    fall: 1.4,
    spring: 1.4
  };
  
  return basePricePerNight * nights * (multipliers[season] || 1);
}

// Usage
function bookProperty(property, checkIn, checkOut, pricingFunction) {
  const nights = calculateNights(checkIn, checkOut);
  const price = pricingFunction(property.basePricePerNight, nights);
  
  return {
    propertyId: property.id,
    checkIn,
    checkOut,
    nights,
    price
  };
}

// Example usage
const booking = bookProperty(
  { id: 'prop1', basePricePerNight: 100 },
  '2023-06-10',
  '2023-06-15',
  calculateWeekendPrice
);
```

### 3. Data-Oriented Design

Data-oriented design focuses on organizing code around the data it processes rather than objects and behaviors:

```javascript
// Instead of complex object hierarchies
// Organize data in simple structures and process with functions

// Data structures
const properties = [
  { id: 'prop1', type: 'apartment', price: 100, location: 'New York' },
  { id: 'prop2', type: 'house', price: 200, location: 'Miami' }
];

const bookings = [
  { id: 'book1', propertyId: 'prop1', userId: 'user1', status: 'confirmed' }
];

// Functions that operate on the data
function findAvailableProperties(location, checkIn, checkOut) {
  // Filter properties by location
  return properties.filter(property => 
    property.location === location &&
    isAvailable(property.id, checkIn, checkOut)
  );
}

function isAvailable(propertyId, checkIn, checkOut) {
  // Check if property is booked during the dates
  return !bookings.some(booking => 
    booking.propertyId === propertyId &&
    booking.status === 'confirmed' &&
    datesOverlap(booking, { checkIn, checkOut })
  );
}

function createBooking(propertyId, userId, checkIn, checkOut) {
  const booking = {
    id: `booking-${Date.now()}`,
    propertyId,
    userId,
    checkIn,
    checkOut,
    status: 'pending'
  };
  
  bookings.push(booking);
  return booking;
}
```

### 4. Minimalist Design

Sometimes the simplest approach is the best. Focus on writing clear, straightforward code with minimal abstractions:

```javascript
// A simple booking function without patterns
function bookProperty(propertyId, userId, checkIn, checkOut) {
  // Validate inputs
  if (!propertyId || !userId || !checkIn || !checkOut) {
    return { success: false, error: 'Missing required fields' };
  }
  
  // Check if dates are valid
  const checkInDate = new Date(checkIn);
  const checkOutDate = new Date(checkOut);
  
  if (checkInDate >= checkOutDate) {
    return { success: false, error: 'Check-out must be after check-in' };
  }
  
  // Check if property exists
  const property = findPropertyById(propertyId);
  if (!property) {
    return { success: false, error: 'Property not found' };
  }
  
  // Check if property is available
  if (!isPropertyAvailable(propertyId, checkIn, checkOut)) {
    return { success: false, error: 'Property not available for these dates' };
  }
  
  // Create booking
  const booking = {
    id: generateBookingId(),
    propertyId,
    userId,
    checkIn,
    checkOut,
    status: 'confirmed',
    totalPrice: calculatePrice(property.price, checkInDate, checkOutDate)
  };
  
  // Save booking
  saveBooking(booking);
  
  // Send confirmation
  sendBookingConfirmation(booking, findUserById(userId), property);
  
  return { success: true, booking };
}
```

## Design Patterns in Web Applications

For your AirBnB-like web application, here are specific patterns that would be particularly useful:

### Frontend Patterns

1. **Component Pattern**: Breaking UI into reusable components (used in React, Vue, Angular)
2. **State Management Patterns**: Flux/Redux for managing application state
3. **Observer Pattern**: For event handling and UI updates
4. **Strategy Pattern**: For different search algorithms or filtering strategies
5. **Decorator Pattern**: For adding features to UI components dynamically
6. **Facade Pattern**: For simplifying complex API calls

### Backend Patterns

1. **Repository Pattern**: For database access
2. **Dependency Injection**: For managing service dependencies
3. **Factory Pattern**: For creating various service objects
4. **Strategy Pattern**: For different payment processing methods
5. **Chain of Responsibility**: For request processing pipelines
6. **Observer Pattern**: For event-driven architectures

### Specific Use Cases in Your AirBnB Clone

#### User Authentication

The **Strategy Pattern** allows different authentication methods (email/password, OAuth, etc.):

```javascript
class AuthStrategy {
  authenticate(credentials) {}
}

class EmailPasswordStrategy extends AuthStrategy {
  authenticate(credentials) {
    // Validate email and password
  }
}

class GoogleOAuthStrategy extends AuthStrategy {
  authenticate(credentials) {
    // Validate Google OAuth token
  }
}

class AuthService {
  constructor(strategy) {
    this.strategy = strategy;
  }
  
  setStrategy(strategy) {
    this.strategy = strategy;
  }
  
  login(credentials) {
    return this.strategy.authenticate(credentials);
  }
}
```

#### Property Listings

The **Builder Pattern** helps construct complex property listings with many optional fields:

```javascript
class PropertyListingBuilder {
  constructor() {
    this.listing = {
      amenities: [],
      images: [],
      rules: {}
    };
  }
  
  setTitle(title) {
    this.listing.title = title;
    return this;
  }
  
  setDescription(description) {
    this.listing.description = description;
    return this;
  }
  
  setPrice(price) {
    this.listing.price = price;
    return this;
  }
  
  setLocation(location) {
    this.listing.location = location;
    return this;
  }
  
  addAmenity(amenity) {
    this.listing.amenities.push(amenity);
    return this;
  }
  
  addImage(image) {
    this.listing.images.push(image);
    return this;
  }
  
  setRule(rule, value) {
    this.listing.rules[rule] = value;
    return this;
  }
  
  build() {
    // Validate required fields
    if (!this.listing.title || !this.listing.price || !this.listing.location) {
      throw new Error('Property listing requires title, price, and location');
    }
    
    return this.listing;
  }
}
```

#### Booking System

The **Facade Pattern** simplifies the complex booking process:

```javascript
class BookingFacade {
  constructor() {
    this.propertyService = new PropertyService();
    this.userService = new UserService();
    this.paymentService = new PaymentService();
    this.notificationService = new NotificationService();
  }
  
  bookProperty(userId, propertyId, dates, paymentInfo) {
    // 1. Check if the user exists and can book
    const user = this.userService.getUser(userId);
    if (!user || user.isBlocked) {
      return { success: false, message: 'User cannot make bookings' };
    }
    
    // 2. Check if the property is available
    const isAvailable = this.propertyService.checkAvailability(propertyId, dates);
    if (!isAvailable) {
      return { success: false, message: 'Property not available for these dates' };
    }
    
    // 3. Calculate the price
    const price = this.propertyService.calculatePrice(propertyId, dates);
    
    // 4. Process the payment
    const paymentResult = this.paymentService.processPayment(userId, price, paymentInfo);
    if (!paymentResult.success) {
      return { success: false, message: 'Payment failed: ' + paymentResult.error };
    }
    
    // 5. Create the booking
    const booking = this.propertyService.createBooking(propertyId, userId, dates, paymentResult.transactionId);
    
    // 6. Send notifications
    this.notificationService.sendBookingConfirmation(booking, user);
    this.notificationService.notifyHost(booking);
    
    return { success: true, booking };
  }
}
```

#### Search Functionality

The **Composite Pattern** allows for complex search criteria:

```javascript
// Search criteria interface
class SearchCriteria {
  meetsCriteria(property) {}
}

// Leaf criteria
class LocationCriteria extends SearchCriteria {
  constructor(location) {
    super();
    this.location = location;
  }
  
  meetsCriteria(property) {
    return property.location.toLowerCase().includes(this.location.toLowerCase());
  }
}

class PriceCriteria extends SearchCriteria {
  constructor(minPrice, maxPrice) {
    super();
    this.minPrice = minPrice;
    this.maxPrice = maxPrice;
  }
  
  meetsCriteria(property) {
    return property.price >= this.minPrice && property.price <= this.maxPrice;
  }
}

class AmenityCriteria extends SearchCriteria {
  constructor(requiredAmenities) {
    super();
    this.requiredAmenities = requiredAmenities;
  }
  
  meetsCriteria(property) {
    return this.requiredAmenities.every(amenity => 
      property.amenities.includes(amenity)
    );
  }
}

// Composite criteria
class AndCriteria extends SearchCriteria {
  constructor(...criteria) {
    super();
    this.criteria = criteria;
  }
  
  meetsCriteria(property) {
    return this.criteria.every(criteria => criteria.meetsCriteria(property));
  }
}

class OrCriteria extends SearchCriteria {
  constructor(...criteria) {
    super();
    this.criteria = criteria;
  }
  
  meetsCriteria(property) {
    return this.criteria.some(criteria => criteria.meetsCriteria(property));
  }
}
```

## Conclusion

Design patterns are invaluable tools for software developers, offering proven solutions to common problems. However, they should be used judiciously, considering the specific needs and context of your application.

For your AirBnB-like web application, a combination of patterns like Facade, Repository, Strategy, and Builder can help you create a modular, maintainable, and flexible system. The Facade pattern, in particular, is an excellent choice for simplifying complex booking processes and hiding the interactions between various components from client code.

As you develop your application, remember to:

1. **Start simple** - Only introduce patterns when they clearly solve a problem
2. **Use patterns as a communication tool** - They provide a common vocabulary for your team
3. **Consider alternatives** - Functional programming or data-oriented approaches might sometimes be simpler
4. **Balance flexibility and complexity** - More flexibility usually means more complexity
5. **Document your patterns** - Make sure other developers understand why and how you're using specific patterns

By thoughtfully applying design patterns and principles, you'll build a more robust and maintainable AirBnB-like platform that can evolve with changing requirements over time.