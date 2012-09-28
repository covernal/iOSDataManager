iOSDataManager
==============

An iOS helper library to make using CoreData, NSUserDefaults, Document loading/saving a little easier.

DataManager's main purpose is to make interacting with CoreData a little easier. It has several helper methods for doing mundane tasks, like querying and updating. It also includes helpers for performing other mundane data tasks, including interacting with NSUserDefaults, loading/saving images, and loading views from nib.
What it DOESN'T do is replace the need for having your AppDelegate setup with a CoreData stack.


Usage:

DataManager is meant to exist as a singleton, accessed through [DataManager SharedDataManager].
In your AppDelegate, in application:didFinishLaunchingWithOptions, add the line:

    [DataManager SharedDataManager].managedObjectContext = [self managedObjectContext];

and that is all you need to do to get DataManager setup.


Assumptions made for the purpose of this ReadMe:
Let's assume you've got a CoreData model created with an Entity called Person.
That Person has the following attributes:
Name
Age
Height
Gender

Let's also assume you've got a reference to the DataManager signleton called dm 
    DataManager * dm = [DataManager SharedDataManager];

CoreData Methods:

DataManager has a handful of simple methods that directly return the results of a fetchedResultController.
Instead of creating a fetchedResultsController, telling to to fetch, and then getting the fetchedObjects, you can call:

    NSArray * allHumans = [dm getResultsWithEntity:@"Person" sortDescriptor:@"Age" batchSize:20];


Or, if you need to perform a predicate, there's one for that too:

    NSArray * women = [dm getResultsWithEntity:@"Person" sortDescriptor:@"Age" sortPredicate:[NSPredicate predicateWithFormat:@"gender != \"men\""] batchSize:20];


DataManager also has methods to return a fetchedResultsController:

    NSFetchedResultsController * controller = [dm fetchedResultsControllerWithEntity:@"Person" sortDescriptor:@"Height" batchSize:20];


NSUserDefaults:

DataManager has two simple wrappers that handle updating the standardUserDefaults.

    [dm setDefaultsUserObject:[some object] forKey:@"anyKey"];
    SomeObject * ThatObjectYouJustSaved = [dm defaulUserObjectForKey:@"anyKey"];


Image saving/loading/caching:

DataManager has methods for doing basic things like saving/loading/deleting an image to the NSDocumentDirectory.

    [dm saveImageToDevice:pictureOfACat withName:@"aCat" extension:@"jpg"];  //jpg and png supported
    [dm loadImageNamed:@"aCat.jpg"];
    [dm removeFile:@"aCat.jpg"];

DataManager also incorporates its own image cache. Using [UIImage imageNamed:] method caches the image, but you have no control over when and how an image is ever uncached, which can lead to memory issues. With DataManager, it will automatically cache any image loaded through loadImageNamed:, which saves time when loading the same image for a second time. Whenever you want to clear the cache, you can call:

    [dm clearImageCache];

Or to remove a specific image:
    [dm removeImageFromImageCache:someUIImage];
    
Or
    [dm removeImageFromImageCacheNamed:@"imageName.jpg"];


Other Helpers:

The rest of the helpers are just simple things that we got tired of having to remember how to do.

You can load a UIView from a nib:

    SomeUIViewClass * view = [dm loadViewFromNib:@"SomeUIViewClassNib" andOwner:self];


