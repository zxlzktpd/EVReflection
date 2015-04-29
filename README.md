# EVReflection
Swift helper library with reflection functions

Run the tests in EVReflectionTests.swift to see EVReflection in action.
EVReflection is used extensively in [EVCloudKitDao](https://github.com/evermeer/EVCloudKitDao)

## Main features of EVReflection:
- Parsing objects based on NSObject to a dictionary. 
- Parsing a dictionary back to an object.
- Creating a class from a string value and get the string value for a class.
- Support NSCoding methods encodeWithCoder and decodeObjectWithCoder
- Supporting Printable, Hashable and Equatable while using all properties. (Support for Set in Swift 1.2)

## Known issues
It's not possible in Swift to use .setObjectForKey for nullable type fiels like Int?. Workaround is using NSNumber? instead

## Using EVReflection in your own App 

'EVReflection' is now available through the dependency manager [CocoaPods](http://cocoapods.org). 
You do have to use cocoapods version 0.36. At this moment this can be installed by executing:

```
[sudo] gem install cocoapods
```

If you have installed cocoapods version 0.36 or later, then you can just add EVReflection to your workspace by adding the folowing 2 lines to your Podfile:

```
use_frameworks!
pod "EVReflection"
```

Version 0.36 of cocoapods will make a dynamic framework of all the pods that you use. Because of that it's only supported in iOS 8.0 or later. When using a framework, you also have to add an import at the top of your swift file like this:

```
import EVReflection
```

If you want support for older versions than iOS 8.0, then you can also just copy the EVReflection.swift to your app. 


## Sample code

```
public class TestObject:NSObject {
var objectValue:String = ""
}
```

```
class EVReflectionTests: XCTestCase {

    func testClassToAndFromString() {
        var theObject = TestObject()
        var theObjectString:String = EVReflection.swiftStringFromClass(theObject)
        NSLog("swiftStringFromClass = \(theObjectString)")
        if var nsobject = EVReflection.swiftClassFromString(theObjectString) {
            NSLog("object = \(nsobject)")
            XCTAssert(true, "Pass")
        } else {
            XCTAssert(false, "Fail")
        }
    }

    func testClassToAndFromDictionary() {
        var theObject = TestObject()
        var theObjectString:String = EVReflection.swiftStringFromClass(theObject)
        theObject.objectValue = "testing"
        var toDict = EVReflection.toDictionary(theObject)
        NSLog("toDictionary = \(toDict)")
        if var nsobject = EVReflection.fromDictionary(toDict, anyobjectTypeString: theObjectString) as? TestObject {
            NSLog("object = \(nsobject), objectValue = \(nsobject.objectValue)")
        }
    }

    func testEquatable() {
        var theObjectA = TestObject2()
        theObjectA.objectValue = "value1"
        var theObjectB = TestObject2()
        theObjectB.objectValue = "value1"
        XCTAssert(theObjectA == theObjectB, "Pass")

        theObjectB.objectValue = "value2"
        XCTAssert(theObjectA != theObjectB, "Pass")
    }

    func testHashable() {
        var theObject = TestObject2()
        theObject.objectValue = "value1"
        var hash1 = theObject.hash
        NSLog("hash = \(hash)")
    }

    func testPrintable() {
        var theObject = TestObject2()
        theObject.objectValue = "value1"
        NSLog("theObject = \(theObject)")
    }

    func testNSCoding() {
        var theObject = TestObject2()
        theObject.objectValue = "value1"

        let fileDirectory =  (NSSearchPathForDirectoriesInDomains(.DocumentDirectory, .UserDomainMask, true)[0] as! NSString)
        var filePath = fileDirectory.stringByAppendingPathComponent("temp.dat")
        NSKeyedArchiver.archiveRootObject(theObject, toFile: filePath)

        var result = NSKeyedUnarchiver.unarchiveObjectWithFile(filePath) as! TestObject2

        XCTAssert(theObject == result, "Pass")
    }
}
```

