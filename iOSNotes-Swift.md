# SWIFT

## Basic
copy and paste into playground to see results

```swift
// Playground - noun: a place where people can play

import UIKit

// Basic
// Hello World !
var str = "Hello, playground"
println(str)

// Int, UInt, Double 64-bit, Float 32-bit

// == Equal, != Not Equal, === Identical, !== Not Identical

// Use let to make a constant and var to make a variabl
// var:mutable, let:immutable
let FLAG=true;
var number=0;
// the complier will infer variable's type. specify the type by writing it after the variable. sparated by a colon
let implicitDouble = 70.0
let explictiDouble: Double = 70

// values are never implicitly converted to anther type. If you need to convert a value to a different type, explicitly make an instance of the desired type.
let label = "The width is "
let width = 94
let widthLabel = label + String(width)

// include values in strings
let apples = 3
let oranges = 5
let fruitSummary = "I have \(apples + oranges) pieces of fruit."

// Create arrays and dictionaries using brackets ([]), and access their elements by writing the index or key in brackets.

var shoppingList : [String] = ["catfish", "water", "tulips", "blue paint"]
shoppingList[1] = "bottle of water"

var occupations = [
    "Malcolm": "Captain",
    "Kaylee": "Mechanic",
]
occupations["Jayne"] = "Public Relations"

var array3D: [[[Int]]] = [[[1,2], [3,4]], [[5,6],[7,8]]]

// To create an empty array or dictionary, use the initializer syntax.
let emptyArray = [String]()
let emptyDictionary = [String: Float]()


// use for-in, for, while, and do-while to make loops
let individualScores = [75, 43, 103, 87, 12]
var teamScore = 0
for score in individualScores {
    if score > 50 {
        teamScore += 3
    } else {
        teamScore += 1
    }
}
println(teamScore)


let vegetable = "red pepper"
switch vegetable {
case "celery":
    let vegetableComment = "Add some raisins and make ants on a log."
case "cucumber", "watercress":
    let vegetableComment = "That would make a good tea sandwich."
case let x where x.hasSuffix("pepper"):
    let vegetableComment = "Is it a spicy \(x)?"
default:
    let vegetableComment = "Everything tastes good in soup."
}


// Creating and Initializeing an Array
var intArray = [Int]()
// Dictionary
let interestingNumbers: [String: [Int]] = [
    "Prime": [2, 3, 5, 7, 11, 13],
    "Fibonacci": [1, 1, 2, 3, 5, 8],
    "Square": [1, 4, 9, 16, 25],
]
var largest = 0
for (kind, numbers) in interestingNumbers {
    for number in numbers {
        if number > largest {
            largest = number
        }
    }
}
println(largest)


var n = 2
while n < 100 {
    n = n * 2
}
println(n)

var m = 2
do {
    m = m * 2
} while m < 100
println(m)

// You can keep an index in a loop—either by using ..< to make a range of indexes or by writing an explicit initialization, condition, and increment.
var firstForLoop = 0
for i in 0..<4 {
    firstForLoop += i
}
println(firstForLoop)

var secondForLoop = 0
for var i = 0; i < 4; ++i {
    secondForLoop += i
}
println(secondForLoop)
// ranges and intervals
// Ranges are good for iterating
var a: Range = 0...5
var b: Range = 0..<5
for i in a {
	println(i)
} 
for i in b {
	println(i)
} 
// Intervals are good for checking
var c: ClosedInterval = 0...5
var d: HalfOpenInterval = 0..<5
if c.contains(5) {
	println("5 is inside the interval")
}
// intervals can be clamped to other
intervals of the same type
d.clamp(3..<7)


// Functions
// Use func to declare a function. Call a function by following its name with a list of arguments in parentheses. Use -> to separate the parameter names and types from the function’s return type.
func greet(name: String, day: String) -> String {
    return "Hello \(name), today is \(day)."
}
greet("Bob", "Tuesday")


// Use a tuple to make a compound value—for example, to return multiple values from a function. The elements of a tuple can be referred to either by name or by number.
// tuples:useful as return type:parameter tuple and return tuple
func calculateStatistics(scores: [Int]) -> (min: Int, max: Int, sum: Int) {
    var min = scores[0]
    var max = scores[0]
    var sum = 0
    
    for score in scores {
        if score > max {
            max = score
        } else if score < min {
            min = score
        }
        sum += score
    }
    
    return (min, max, sum)
}
let statistics = calculateStatistics([5, 3, 100, 3, 9])
statistics.sum
statistics.2


// Functions can also take a variable number of arguments, collecting them into an array.
func sumOf(numbers: Int...) -> Int {
    var sum = 0
    for number in numbers {
        sum += number
    }
    return sum
}
sumOf()
sumOf(42, 597, 12)


// Functions can be nested.
func returnFifteen() -> Int {
    var y = 10
    func add() {
        y += 5
    }
    add()
    return y
}
returnFifteen()


// Functions are a first-class type. This means that a function can return another function as its value.
func makeIncrementer() -> (Int -> Int) {
    func addOne(number: Int) -> Int {
        return 1 + number
    }
    return addOne
}
var increment = makeIncrementer()
increment(7)


// A function can take another function as one of its arguments.
func hasAnyMatches(list: [Int], condition: Int -> Bool) -> Bool {
    for item in list {
        if condition(item) {
            return true
        }
    }
    return false
}
func lessThanTen(number: Int) -> Bool {
    return number < 10
}
var numbers = [20, 19, 7, 12]
hasAnyMatches(numbers, lessThanTen)

// External Parameter Names
func someFunction(externalParameterName localParameterName: Int) {
    println(localParameterName);
}

// ({})


// Objects and Classes
// class className:superClass, adoptedProtocols{}
// Create an instance of a class by putting parentheses after the class name. Use dot syntax to access the properties and methods of the instance.
// subclass and overrride
// getter and setter
// willSet and didSet
class NamedShape {
    var numberOfSides: Int = 0
    var name: String
    
    init(name: String) {
        self.name = name
    }
    
    func simpleDescription() -> String {
        return "A shape with \(numberOfSides) sides."
    }
}

class Square: NamedShape {
    var sideLength: Double
    
    init(sideLength: Double, name: String) {
        self.sideLength = sideLength
        super.init(name: name)
        numberOfSides = 4
    }
    
    var p : Double {
        get {
            return sideLength
        }
        set {
            sideLength = newValue
        }
    }
    
    func area() ->  Double {
        return sideLength * sideLength
    }
    
    override func simpleDescription() -> String {
        return "A square with sides of length \(sideLength)."
    }
}
var test = Square(sideLength: 5.2, name: "my test square")
test.area()
test.simpleDescription()


// When working with optional values, you can write ? before operations like methods, properties, and subscripting. If the value before the ? is nil, everything after the ? is ignored and the value of the whole expression is nil. Otherwise, the optional value is unwrapped, and everything after the ? acts on the unwrapped value. In both cases, the value of the whole expression is an optional value.
// An optional says: There is a value and it equals x, or There isn't a value at all
var optionalSquare: Square? = Square(sideLength: 2.5, name: "optional square")
var sideLength = optionalSquare?.sideLength

// enum
enum Suit {
    case Spades, Hearts, Diamonds, Clubs
    func simpleDescription() -> String {
        switch self {
        case .Spades:
            return "spades"
        case .Hearts:
            return "hearts"
        case .Diamonds:
            return "diamonds"
        case .Clubs:
            return "clubs"
        }
    }
}
let hearts = Suit.Hearts
let heartsDescription = hearts.simpleDescription()

// Use struct to create a structure. Structures support many of the same behaviors as classes, including methods and initializers. One of the most important differences between structures and classes is that structures are always copied when they are passed around in your code, but classes are passed by reference.
struct Card {
    var rank: Int
    var suit: Suit
    func simpleDescription() -> String {
        return "The \(rank) of \(suit.simpleDescription())"
    }
}
let threeOfSpades = Card(rank: 1, suit: .Spades)
let threeOfSpadesDescription = threeOfSpades.simpleDescription()


// Protocols, class, enumerations, structs
// Protocols:similar to objective-c,java interfaces
protocol ExampleProtocol {
    var simpleDescription: String { get }
    mutating func adjust()
}

// class:good for shared data,large data or as a resource handle:long lived objects,controller and view objects,class hierarchies,objects in the true sense
class SimpleClass: ExampleProtocol {
    var simpleDescription: String = "A very simple class."
    var anotherProperty: Int = 69105
    func adjust() {
        simpleDescription += "  Now 100% adjusted."
    }
}
var a = SimpleClass()
a.adjust()
let aDescription = a.simpleDescription

// Structs:collection of named properties:shorted lived objects,objects that are creatd often,model objects,data capsules
struct SimpleStructure: ExampleProtocol {
    var simpleDescription: String = "A simple structure"
    mutating func adjust() {
        simpleDescription += " (adjusted)"
    }
}
var b = SimpleStructure()
b.adjust()
let bDescription = b.simpleDescription
// Notice the use of the mutating keyword in the declaration of SimpleStructure to mark a method that modifies the structure. The declaration of SimpleClass doesn’t need any of its methods marked as mutating because methods on a class can always modify the class.
// Use extension to add functionality to an existing type, such as new methods and computed properties. You can use an extension to add protocol conformance to a type that is declared elsewhere, or even to a type that you imported from a library or framework.
extension Int: ExampleProtocol {
    var simpleDescription: String {
        return "The number \(self)"
    }
    mutating func adjust() {
        self += 42
    }
}
/* ------------------ */
protocol Stringifyable {
  func stringify() -> String
} 
struct MapPoint {
  var longitude: Double
  var latitude: Double
  func rhumbDistance(other: MapPoint) -> Double {
  let dLong = self.longitude - other.longitude
  let dLat = self.latitude - other.latitude
  return sqrt(dLong * dLong + dLat * dLat)
} } 
extension MapPoint: Stringifyable {
  func stringify() -> String {
  return "(\(longitude); \(latitude))"
  }
}
/* ------------------ */

// Generics  Write a name inside angle brackets to make a generic function or type.
func repeat<Item>(item: Item, times: Int) -> [Item] {
    var result = [Item]()
    for i in 0..<times {
        result.append(item)
    }
    return result
}
repeat("knock", 4)


```