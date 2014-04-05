# mophie Objective-C Style Guide [Draft]

This style guide outlines the mophie's Objective-C coding conventions.

## Introduction

This guide was created to help facilitate readability and encourage best-practices for mophie employees when they are writing Objective-C code. This document is currently a draft. Employees and community members are encouraged to submit their suggestions via pull requests or tickets on GitHub.

## Credits

The creation of this style guide was made possible by (and is forked from) the [RayWenderlich.com style guide](https://github.com/raywenderlich/objective-c-style-guide).

## Existing Standards

Objective-C programmers are encouraged to take the time to read the documents below. They provide a solid foundation of Objective-C conventions. Anything not covered in this guide is likely covered in one of the links below:

* [The Objective-C Programming Language](http://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
* [Cocoa Fundamentals Guide](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CocoaFundamentals/Introduction/Introduction.html)
* [Coding Guidelines for Cocoa](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
* [iOS App Programming Guide](http://developer.apple.com/library/ios/#documentation/iphone/conceptual/iphoneosprogrammingguide/Introduction/Introduction.html)

## Table of Contents

* [Code Organization](#code-organization)
* [Spacing](#spacing)
* [Comments](#comments)
* [Naming](#naming)
  * [Underscores](#underscores)
* [Methods](#methods)
* [Variables](#variables)
* [Property Attributes](#property-attributes)
* [Dot-Notation Syntax](#dot-notation-syntax)
* [Literals](#literals)
* [Constants](#constants)
* [Enumerated Types](#enumerated-types)
* [Case Statements](#case-statements)
* [Booleans](#booleans)
* [Conditionals](#conditionals)
  * [Ternary Operator](#ternary-operator)
* [Init Methods](#init-methods)
* [Class Constructor Methods](#class-constructor-methods)
* [CGRect Functions](#cgrect-functions)
* [Golden Path](#golden-path)
* [Error Handling](#error-handling)
* [Singletons](#singletons)
* [Line Breaks](#line-breaks)
* [Xcode Project](#xcode-project)


## Code Organization

Use `#pragma mark -` to categorize methods in functional groupings and protocol/delegate implementations following this general structure. Methods in each group should be listed alphabetically, unless execution order is more appropriate (i.e. lifecycle methods).

```objc
#pragma mark - Object Lifecycle

- (instancetype)init {}
- (void)dealloc {}


#pragma mark - Object Control

- (id)customProperty {}
- (NSString *)description {}
- (void)didReceiveMemoryWarning {}
- (void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary *)change context:(void *)context {}
- (void)setCustomProperty:(id)value {}


#pragma mark - View Lifecycle

- (void)viewDidLoad {}
- (void)viewWillAppear:(BOOL)animated {}


#pragma mark - View Control

- (IBAction)submitData:(id)sender {}
- (void)refreshSomeButtons;


#pragma mark - ANOtherObject Notifications
#pragma mark - NSCopying
#pragma mark - UITableViewDataSource
#pragma mark - UITableViewDelegate
#pragma mark - UITextFieldDelegate
```

## Spacing

* Indent using 4 spaces. Never indent with tabs. Be sure to set this preference in Xcode.
* Method braces and other braces (`if`/`else`/`switch`/`while` etc.) always open on the same line as the statement but close on a new line.

**Preferred:**
```objc
if (user.isHappy) {
    //Do something
} else {
    //Do something else
}
```

**Not Preferred:**
```objc
if (user.isHappy)
{
    //Do something
}
else {
    //Do something else
}
```

* There should be exactly two blank lines between methods to aid in visual clarity and organization. Whitespace within methods should separate functionality, but often there should probably be new methods.
* Prefer using auto-synthesis. But if necessary, `@synthesize` and `@dynamic` should each be declared on new lines in the implementation.
* Colon-aligning method invocation should often be avoided.  There are cases where a method signature may have >= 3 colons and colon-aligning makes the code more readable. Please do **NOT** however colon align methods containing blocks because Xcode's indenting makes it illegible.

**Preferred:**

```objc
// blocks are easily readable
[UIView animateWithDuration:1.0 animations:^{
    // something
} completion:^(BOOL finished) {
    // something
}];
```

**Not Preferred:**

```objc
// colon-aligning makes the block indentation hard to read
[UIView animateWithDuration:1.0
                 animations:^{
                     // something
                 }
                 completion:^(BOOL finished) {
                     // something
                 }];
```

## Comments

When they are needed, comments should be used to explain **why** a particular piece of code does something. Any comments that are used must be kept up-to-date or deleted.

Block comments should generally be avoided, as code should be as self-documenting as possible, with only the need for intermittent, few-line explanations. *Exception: This does not apply to those comments used to generate documentation.*

## Naming

Apple naming conventions should be adhered to wherever possible, especially those related to [memory management rules](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html) ([NARC](http://stackoverflow.com/a/2865194/340508)).

Long, descriptive method and variable names are good.

**Preferred:**

```objc
UIButton *settingsButton;
```

**Not Preferred:**

```objc
UIButton *setBut;
```

Classes belonging to a project should not have a letter prefix, unless those classes are part of a reusable library, in which case they should be prefixed appropriately to reference that library's name. For any generic mophie reusable code, the prefix 'MO' should be used.

Constants should be camel-case with all words capitalized. For frameworks or libraries, they should be prefixed by the related class name for clarity.

**Preferred:**

```objc
static NSTimeInterval const MOFadeAnimationDuration = 0.3;
static NSTimeInterval const SyncManagerRetryTime = 2.7;
```

**Not Preferred:**

```objc
static NSTimeInterval const fadetime = 0.3;
static NSTimeInterval const kSyncManagerRetryTime = 2.7;
```

Properties should be camel-case with the leading word being lowercase. Use auto-synthesis for properties rather than manual @synthesize statements unless you have good reason.

**Preferred:**

```objc
@property (strong, nonatomic) NSString *descriptiveVariableName;
for (NSUInteger index = 0; index < count; index++) {}
```

**Not Preferred:**

```objc
id varnm;
for (int i = 0; i < count; i++) {}
```

### Underscores

Instance variables should always be prefixed with a single underscore `id _anExample`, not two `id __aBadExample` or appended `id anotherBadExample_`.

When accessing and mutating properties, always use the backing instance variable unless you have a good reason to call `self.` (eg. to make sure KVO methods are fired).

Local variables should not contain underscores.

## Methods

In method signatures, there should be a space after the method type (-/+ symbol). There should be a space between the method segments (matching Apple's style).  Always include a keyword and be descriptive with the word before the argument which describes the argument.

The usage of the word "and" is reserved.  It should not be used for multiple parameters as illustrated in the `initWithWidth:height:` example below.

**Preferred:**
```objc
- (void)setExampleText:(NSString *)text image:(UIImage *)image;
- (void)sendAction:(SEL)aSelector to:(id)anObject forAllCells:(BOOL)flag;
- (id)viewWithTag:(NSInteger)tag;
- (instancetype)initWithWidth:(CGFloat)width height:(CGFloat)height;
```

**Not Preferred:**

```objc
-(void)setT:(NSString *)text i:(UIImage *)image;
- (void)sendAction:(SEL)aSelector :(id)anObject :(BOOL)flag;
- (id)taggedView:(NSInteger)tag;
- (id)initWithWidth:(CGFloat)width andHeight:(CGFloat)height;
- (instancetype)initWith:(int)width and:(int)height;  // Never do this.
```

## Variables

Variables should be named as descriptively as possible, including index variables in `for()` loops.

Asterisks indicating pointers belong with the variable, e.g., `NSString *goodDefinition` not `NSString* badDefinition` or `NSString * badDefinition`.

Private properties should never be declared unless desired functionality cannot be achieved using instance variables.


**Preferred:**

```objc
@interface MOExample : NSObject
{
    NSString *_tutorialName;
}

@end
```

**Not Preferred:**

```objc
@interface MOTutorial : NSObject

@property (strong, nonatomic) NSString *tutorialName;

@end
```

When declaring and instantiating variables, never assign them to their default values.

**Preferred:**

```objc
NSString *newString;
BOOL isReady;
id object;
NSUInteger index;
CGImageRef imageRef = NULL;

```

**Not Preferred:**

```objc
NSString *newString = nil;
BOOL isReady = NO;
id object = nil;
NSUInteger index = 0;
CGImageRef imageRef; // May actually end up pointing to something if not assigned to NULL.
```


## Property Attributes

Property attributes should be explicitly listed, even when not technically required by the compiler.  The order of properties should be storage, atomicity, `readonly` (if applicable), and finally `getter=`. This odrder is consistent with the automatically generated code when connecting UI elements from Interface Builder.

**Preferred:**

```objc
@property (weak, nonatomic) IBOutlet UIView *containerView;
@property (strong, nonatomic) NSString *tutorialName;
@property (strong, nonatomic, readonly) NSArray *tutorialSlides;
@property (strong, nonatomic, getter=isFinished) BOOL finished;
```

**Not Preferred:**

```objc
@property (nonatomic, weak) IBOutlet UIView *containerView;
@property (nonatomic) NSString *tutorialName;
@property (getter=isFinished) BOOL finished;
```



## Dot-Notation Syntax

Dot syntax is purely a convenient wrapper around accessor method calls. When you use dot syntax, the property is still accessed or changed using getter and setter methods.  Read more [here](https://developer.apple.com/library/ios/documentation/cocoa/conceptual/ProgrammingWithObjectiveC/EncapsulatingData/EncapsulatingData.html)

Dot-notation should **always** be used for accessing and mutating properties, as it makes code more concise. Bracket notation is preferred in all other instances. Never use dot notation to call or execute a method.

**Preferred:**
```objc
NSInteger arrayCount = [self.array count];
view.backgroundColor = [UIColor orangeColor];
[UIApplication sharedApplication].delegate;
```

**Not Preferred:**
```objc
NSInteger arrayCount = self.array.count;
[view setBackgroundColor:[UIColor orangeColor]];
UIApplication.sharedApplication.delegate;
```

## Literals

`NSString`, `NSDictionary`, `NSArray`, and `NSNumber` literals should be used whenever creating immutable instances of those objects. Pay special care that `nil` values can not be passed into `NSArray` and `NSDictionary` literals, as this will cause a crash.

**Preferred:**

```objc
NSArray *names = @[@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul"];
NSDictionary *productManagers = @{@"iPhone": @"Kate", @"iPad": @"Kamal", @"Mobile Web": @"Bill"};
NSNumber *shouldUseLiterals = @YES;
NSNumber *buildingStreetNumber = @10018;
```

**Not Preferred:**

```objc
NSArray *names = [NSArray arrayWithObjects:@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul", nil];
NSDictionary *productManagers = [NSDictionary dictionaryWithObjectsAndKeys: @"Kate", @"iPhone", @"Kamal", @"iPad", @"Bill", @"Mobile Web", nil];
NSNumber *shouldUseLiterals = [NSNumber numberWithBool:YES];
NSNumber *buildingStreetNumber = [NSNumber numberWithInteger:10018];
```

## Constants

Constants are preferred over in-line string literals or numbers, as they allow for easy reproduction of commonly used variables and can be quickly changed without the need for find and replace. Constants should be declared as `static` constants and not `#define`s unless explicitly being used as a macro.

**Preferred:**

```objc
static NSString *const AboutViewControllerCompanyName = @"mophie.com";
static CGFloat const ImageThumbnailHeight = 50.0;
```

**Not Preferred:**

```objc
#define kCompanyName @"mophie.com"
#define thumbnailHeight 2
```

## Enumerated Types

When using `enum`s, always use the new fixed underlying type specification because it has stronger type checking and code completion. The SDK now includes a macro to facilitate and encourage use of fixed underlying types: `NS_ENUM()`

**For Example:**

```objc
typedef NS_ENUM(NSInteger, LeftMenuTopItemType) {
    LeftMenuTopItemMain,
    LeftMenuTopItemShows,
    LeftMenuTopItemSchedule
};
```

Older k-style constant definitions should be **avoided** unless writing CoreFoundation C code (unlikely).

**Not Preferred:**

```objc
enum GlobalConstants {
    kMaxPinSize = 5,
    kMaxPinCount = 500,
};
```


## Case Statements

Braces are not required for case statements, unless enforced by the complier. When a case contains more than one line, braces must be added.

```objc
switch (condition) {
    case 1:
        // ...
        break;
    case 2: {
        // ...
        // Multi-line example using braces
        break;
    }
    case 3:
        // ...
        break;
    default:
        // ...
        break;
}

```

There are times when the same code can be used for multiple cases, and a fall-through should be used.  A fall-through is the removal of the 'break' statement for a case thus allowing the flow of execution to pass to the next case value.  A fall-through should be commented for coding clarity.

```objc
switch (condition) {
    case 1:
    // ** fall-through! **
    case 2:
        // code executed for values 1 and 2
        break;
    default:
        // ...
        break;
}

```

When using an enumerated type for a switch, 'default' is not needed.   For example:

```objc
LeftMenuTopItemType menuType = LeftMenuTopItemMain;

switch (menuType) {
    case LeftMenuTopItemMain:
        // ...
        break;
    case LeftMenuTopItemShows:
        // ...
        break;
    case LeftMenuTopItemSchedule:
        // ...
        break;
}
```


## Booleans

Objective-C uses `YES` and `NO`.  Therefore `true` and `false` should only be used for CoreFoundation, C or C++ code.  Since `nil` resolves to `NO` it is unnecessary to compare it in conditions. Never compare something directly to `YES`, because `YES` is defined to 1 and a `BOOL` can be up to 8 bits.

This allows for more consistency across files and greater visual clarity.

**Preferred:**

```objc
if (someObject) {}
if (![anotherObject boolValue]) {}
```

**Not Preferred:**

```objc
if (someObject == nil) {}
if ([anotherObject boolValue] == NO) {}
if (isAwesome == YES) {} // Never do this.
if (isAwesome == true) {} // Never do this.
```

If the name of a `BOOL` property is expressed as an adjective, the property can omit the “is” prefix but specifies the conventional name for the get accessor, for example:

```objc
@property (assign, getter=isEditable) BOOL editable;
```
Text and example taken from the [Cocoa Naming Guidelines](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingIvarsAndTypes.html#//apple_ref/doc/uid/20001284-BAJGIIJE).

## Conditionals

Conditional bodies should always use braces even when a conditional body could be written without braces (e.g., it is one line only) to prevent errors. These errors include adding a second line and expecting it to be part of the if-statement. Another, [even more dangerous defect](http://programmers.stackexchange.com/a/16530) may happen where the line "inside" the if-statement is commented out, and the next line unwittingly becomes part of the if-statement. In addition, this style is more consistent with all other conditionals, and therefore more easily scannable.

**Preferred:**
```objc
if (!error) {
    return success;
}
```

**Not Preferred:**
```objc
if (!error)
    return success;
```

or

```objc
if (!error) return success;
```

### Ternary Operator

The Ternary operator, `?:` , should only be used when it increases clarity or code neatness. A single condition is usually all that should be evaluated. Evaluating multiple conditions is usually more understandable as an `if` statement, or refactored into instance variables. In general, the best use of the ternary operator is during assignment of a variable and deciding which value to use.

Non-boolean variables should be compared against something, and parentheses are added for improved readability.  If the variable being compared is a boolean type, then no parentheses are needed.

**Preferred:**
```objc
NSInteger value = 5;
result = (value != 0) ? x : y;

BOOL isHorizontal = YES;
result = isHorizontal ? x : y;
```

**Not Preferred:**
```objc
result = a > b ? x = c > d ? c : d : y;
```

## Init Methods

Init methods should follow the convention provided by Apple's generated code template.  A return type of 'instancetype' should also be used instead of 'id'.

```objc
- (instancetype)init {
    self = [super init];

    if (self) {
        // Setup object
    }

    return self;
}
```


## Class Constructor Methods

Where class constructor methods are used, these should always return type of 'instancetype' and never 'id'. This ensures the compiler correctly infers the result type.

```objc
@interface Airplane
+ (instancetype)airplaneWithType:(AirplaneType)type;
@end
```

More information on instancetype can be found on [NSHipster.com](http://nshipster.com/instancetype/).

## CGRect Functions

When accessing the `x`, `y`, `width`, or `height` of a `CGRect`, always use the [`CGGeometry` functions](http://developer.apple.com/library/ios/#documentation/graphicsimaging/reference/CGGeometry/Reference/reference.html) instead of direct struct member access. From Apple's `CGGeometry` reference:

> All functions described in this reference that take CGRect data structures as inputs implicitly standardize those rectangles before calculating their results. For this reason, your applications should avoid directly reading and writing the data stored in the CGRect data structure. Instead, use the functions described here to manipulate rectangles and to retrieve their characteristics.

**Preferred:**

```objc
CGRect frame = self.view.frame;

CGFloat x = CGRectGetMinX(frame);
CGFloat y = CGRectGetMinY(frame);
CGFloat width = CGRectGetWidth(frame);
CGFloat height = CGRectGetHeight(frame);
CGRect frame = CGRectMake(0.0, 0.0, width, height);
```

**Not Preferred:**

```objc
CGRect frame = self.view.frame;

CGFloat x = frame.origin.x;
CGFloat y = frame.origin.y;
CGFloat width = frame.size.width;
CGFloat height = frame.size.height;
CGRect frame = (CGRect){ .origin = CGPointZero, .size = frame.size };
```

## Golden Path

When coding with conditionals, the left hand margin of the code should be the "golden" or "happy" path.  That is, don't nest `if` statements.  Multiple return statements are OK, and preferred.

**Preferred:**

```objc
- (void)someMethod
{
    if (![someOther boolValue] || !isReady) {
        return;
    }

    // Do something important
}
```

**Not Preferred:**

```objc
- (void)someMethod
{
    if ([someOther boolValue]) {
        if (isReady) {
            //Do something important
        }
    }
}
```

## Error handling

When methods return an error parameter by reference, switch on the returned value, not the error variable.

**Preferred:**
```objc
NSError *error;
if (![self trySomethingWithError:&error]) {
    // Handle Error
}
```

**Not Preferred:**
```objc
NSError *error;
[self trySomethingWithError:&error];
if (error) {
    // Handle Error
}
```

Some of Apple’s APIs write garbage values to the error parameter (if non-NULL) in successful cases, so switching on the error can cause false negatives (and subsequently crash).


## Singletons

Singleton objects should use a thread-safe pattern for creating their shared instance.
```objc
+ (SomeSingleton)shared
{
    static id shared = nil;
    static dispatch_once_t onceToken;

    dispatch_once(&onceToken, ^{
        shared = [[self alloc] init];
    });

    return shared;
}
```
This will prevent [possible and sometimes prolific crashes](http://cocoasamurai.blogspot.com/2011/04/singletons-your-doing-them-wrong.html).


## Line Breaks

For readability (and printability) header files should not exceed 100 columns per line. To aid with hard-wrapping lines, enable the Page guide in Xcode -> Preferences -> Text Editing. Long invocations or method definitions should be wrapped unless the method has block parameters.

**For Example:**
```objc
[[NSNotificationCenter defaultCenter] addObserver:self
                                         selector:@selector(cameraSyncStatusDidChangeNotification:)
                                             name:CameraSyncStatusDidChangeNotificationName
                                           object:nil];
```


## Xcode Project / Directory Structure

The physical files should be kept in sync with the Xcode project files in order to avoid file sprawl. Any Xcode groups created should be reflected by folders in the filesystem.

Classes are generally grouped in folders by their parent class type in the Source/ folder. Never use spaces in folder or group names.

The group/folder structure should follow this general structure:

```
SomeProject/
    Source/
        Objects/
        ViewControllers/
        ...
        main.m
        Prefix.pch
    Resources/
        Audio/
        DataModels/
        Graphics.xcasset
        PropertyLists/
            Info.plist
        ...
    Libraries/
        Third-PartyLibA/
        Third-PartyLibB/
        ...
    Pods/
        ...
    SomeProject.xcproj
    SomeProject.xcworkspace
```

When possible, always turn on "Treat Warnings as Errors" in the target's Build Settings and enable as many [additional warnings](http://boredzo.org/blog/archives/2009-11-07/warnings) as possible. If you need to ignore a specific warning, use [Clang's pragma feature](http://clang.llvm.org/docs/UsersManual.html#controlling-diagnostics-via-pragmas).


# Other Objective-C Style Guides

Still not a style guide you like? Here are some more:

* [Google](http://google-styleguide.googlecode.com/svn/trunk/objcguide.xml)
* [GitHub](https://github.com/github/objective-c-conventions)
* [Adium](https://trac.adium.im/wiki/CodingStyle)
* [Sam Soffes](https://gist.github.com/soffes/812796)
* [CocoaDevCentral](http://cocoadevcentral.com/articles/000082.php)
* [Luke Redpath](http://lukeredpath.co.uk/blog/my-objective-c-style-guide.html)
* [Marcus Zarra](http://www.cimgf.com/zds-code-style-guide/)
