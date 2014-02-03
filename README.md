Coding-for-iOS
==============

A walkthrough of best practices. This is a collection of the what you find on internet put together in a tidy page. I take no credit for anything other than collecting and ammending. Yes, give me some love for that. 



## Constants



### Public constants

.h

```
extern const CGFloat ASHHeaderViewHeight;
```
.m
```
const CGFloat ASHHeaderViewHeight = 44.0;
```



### Private constants

In top of your implementation-file:

```
const CGFloat ASHHeaderViewHeight = 44.0;
```


## Naming

### Classes
Use 3 letter-prefixes. 

**Why?** The probability of someone else using a two-letter prefix is very high. In fact we've already experienced it with *AG*. Cocoapods is steadily growing.

### Categories

Prefix everything - including the setters. 
```
@interface UIScrollView (AGSAnimation)

@property (nonatomic, strong, readwrite, setter=ags_setOffsetAnimation:) AGSAnimation *ags_offsetAnimation;

- (void)ags_setContentOffset:(CGPoint)targetOffset
              timingFunction:(double(^)(double p))timingFunction
                    duration:(NSTimeInterval)duration
                  onComplete:(void(^)(BOOL cancelled))onComplete;
               
@end
```

#### Category-name

The prefix can concist of your project/company OR the purpose of the category.  

E.g. `AGS` from `Agens`  
E.g. `COS` from `ContentOffsetAnimation` 

The latter one is relevant when writing small categories. 

```
@interface UIScrollView (AGSAnimation)

@property (nonatomic, strong, readwrite, setter=ags_setOffsetAnimation:) AGSAnimation *ags_offsetAnimation;

- (void)ags_setContentOffset:(CGPoint)targetOffset
              timingFunction:(double(^)(double p))timingFunction
                    duration:(NSTimeInterval)duration
                  onComplete:(void(^)(BOOL cancelled))onComplete;
               
@end
```

### Properties

I suggest grouping using bottom-up hirearchy because of the natural grouping.

```
@property (nonatomic, strong) labelTitle;
@property (nonatomic, strong) labelDescription;
@property (nonatomic, strong) viewFail;
@property (nonatomic, strong) viewLoading;
```


## Categories

Prefix everything - including the setters. 
```
@interface UIScrollView (AGSAnimation)

@property (nonatomic, strong, readwrite, setter=ags_setOffsetAnimation:) AGSAnimation *ags_offsetAnimation;

- (void)ags_setContentOffset:(CGPoint)targetOffset
              timingFunction:(double(^)(double p))timingFunction
                    duration:(NSTimeInterval)duration
                  onComplete:(void(^)(BOOL cancelled))onComplete;
               
@end
```









## Properties



### Readonly in @interface,  readwrite in @implementation

.h
```
@interface AGSFirstViewController ()

@property (nonatomic, readonly) NSString *magicString;

@end
```
.m

```

@interface AGSFirstViewController ()

@property (nonatomic, strong, readwrite) NSString *magicString;

@end
```
**When?**  You want to create an immutable object.

**Why?** It is considered a good practice to use the getters and setters instead of ivars themselves. And you need to write to the property. 




### When to use copy

As a rule of thumb: Use copy whenever there's a immutable/mutable-pair (e.g. NSArray, NSDictionary, NSString).

**Why?** Consider the following example not using `copy` and how it all gets safe whilst using `copy`:

```
@interface Book : NSObject
 
@property (strong, nonatomic) NSString *title;
 
@end

...

- (void)stringExample 
{
    NSMutableString *bookTitle = [NSMutableString stringWithString:@"Best book ever"];
    Book *book = [[Book alloc] init];
    book.title = bookTitle;
    [bookTitle setString:@"Worst book ever"];
    NSLog(@"book title %@", book.title);
 
}
```
Source: http://ios-blog.co.uk/tutorials/quick-tips/use-copy-for-nsstring-properties/

Argument: I would never do that if I knew the property was strong  
Answer: Well, other devs might :) You are better off safe-guarding 

Argument: Why use `-[NSMutableString setString:]` at all?  
Answer: Well, again, don't you want your class to be safe from malicious code like that?







## Protocols



### Protocols supported – public

```
// *.h-file

@interface AGSCar : NSObject <AGSVeichle>
@end
```

### Protocols supported – private

```
// *.m-file

@interface AGSFirstViewController () <UITableViewDelegate, UITableViewDataSource>
@end
```

**Why?** No need to tell everyone you support the `UITableViewDelegate`-protocol. Keep your interface clean.









## Anti-patterns

### Don't declare ivars

```
@interface MyClass : NSObject {
    BOOL someVariable;
}
@end
```

### Never cast immutable to mutable

```
- (NSArray *)fellowCitizens
{
	NSMutableArray *citizens = [NSMutableArray array];
	[citizens addObject:@"Truls"];	
	[citizens addObject:@"Jonas"];
	return citizens;
}

- (void)main
{
	NSArray *citizens = [self fellowCitizens];
	if([citizens isKindOfClass:[NSMutableArray class]]) 
	{
		[citizens removeObject:@"Jonas"]; // we don't need this guy	
	}
}

```
**Why?** Of course this is very wrong, but if we can't trust developers not doing this, then code will be very cluttered! Imagine having to return a copy of citizens just to avoid people from misusing. What if the fellowCitizens where to change into something like this?

```
- (NSArray *)fellowCitizens
{
    static NSMutableArray *citizens;
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        citizens = [NSMutableArray array];
        [citizens addObject:@"Truls"];
        [citizens addObject:@"Jonas"];
    });
	return citizens;
}

```










## Sources

http://ios-blog.co.uk/tutorials/quick-tips/use-copy-for-nsstring-properties/

http://ashfurrow.com/blog/structuring-modern-objective-c



[![Agens | Digital craftsmanship](http://static.agens.no/images/agens_logo_w_slogan_avenir_small.png)](http://agens.no/)

