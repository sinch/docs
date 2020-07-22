---
title: Build an iOS Twitter App to Call Your Friends
excerpt: >-
  In this tutorial, you will learn how to make an app that lets users call
  people who they follow on Twitter. We’ll do this using Sinch app-to-app
  calling to privately place calls with the user’s Twitter handle.
---
Twitter is a great way for people to connect, but it’s a shame that they can’t connect with voice calling. Sure, you can tag each other or direct message (DM) back and forth, but that can get a bit impersonal at times. In this tutorial, we’ll be making an app that lets users call people who they follow on Twitter. We’ll do this using Sinch [app-to-app calling](https://www.sinch.com/products/voice/data-calling/) to privately place calls with the user’s Twitter handle.

To get the most out of this tutorial, you’ll need to be familiar with the following areas:
![Login.png](images\c558fa2-Login.png)


![Call-in.png](images\a1edafc-Call-in.png)

## 1. Setup

Create an app on your [Sinch Dashboard](https://portal.sinch.com/#/login). Take note of the application key and secret, as you will need them later.

The starter project for this tutorial can be found on [GitHub](https://github.com/sinch/ios-call-your-twitter-friends). Once you’ve downloaded that, go ahead and install the necessary pods using CocoaPods with the included **Podfile**. You can do so through Terminal with the command `pod install`. Now there should be an XCode workspace that you can use.

## 2. Twitter integration

Here is an overview of how we’ll set up Twitter in this app. To begin, we’ll request permission to use the Twitter account associated with the user’s device. Once this permission has been granted, the user will be presented with a list of people whom they follow. Later on, we’ll let these entries be selectable and the selected person be called.

Now go to **LoginViewController.m**. Locate the method called `ContinueAction` and add to the method body so it looks as follows:

```objectivec
- (IBAction)ContinueAction:(id)sender {
    ACAccountStore *accountStore = [[ACAccountStore alloc] init];
    ACAccountType *accountType = [accountStore accountTypeWithAccountTypeIdentifier:ACAccountTypeIdentifierTwitter];
    [accountStore requestAccessToAccountsWithType:accountType options:nil completion:^(BOOL granted, NSError *error){
        dispatch_async(dispatch_get_main_queue(), ^{
            if (granted) {
                NSArray *accounts = [accountStore accountsWithAccountType:accountType];
                if (accounts.count > 0)
                {
                    ACAccount *twitterAccount = [accounts objectAtIndex:0];

                    // 1

                    [self performSegueWithIdentifier:@"showMaster" sender:twitterAccount];

                }
                else {
                    NSLog(@"no accounts");
                }
            } else {
                NSLog(@"No access granted");
            }

        });
    }];
}
```

Here, all we do is request Twitter permissions once you click the “Continue” button. If there are multiple Twitter accounts on the same device, we default to the first one. We pass the Twitter account as a sender for a segue so we can use it later on to request a list of people whom the user follows.

Now, locate `prepareForSegue:sender:`. We’ll need to set the user’s Twitter account, so let’s add some code to do that:

```objectivec
- (void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender {
    if ([[segue identifier] isEqualToString:@"showMaster"]) {
        [[segue destinationViewController] setTwitterAccount:sender];
    }
}
```

You’ll get an error since we don’t have a twitterAccount property right now. Let’s change that. Add the following property to the interface in **MasterViewController.h**:

```objectivec
@property (strong, nonatomic) ACAccount *twitterAccount;
```

Great, now the user will be asked to grant the app access to Twitter. If access is granted, the app will move to the next screen.

Next, get a list of everyone the user follows.

Head to **MasterViewController.m**. Let’s add a method to get everyone the user follows using a Twitter API request.

```objectivec
- (void) getFriends {
    SLRequest *twitterInfoRequest = [SLRequest requestForServiceType:SLServiceTypeTwitter requestMethod:SLRequestMethodGET URL:[NSURL URLWithString:@"https://api.twitter.com/1.1/friends/ids.json?"] parameters:[NSDictionary dictionaryWithObjectsAndKeys:[NSString stringWithFormat:@"%@", self.twitterAccount.username], @"screen_name", @"-1", @"cursor", nil]];
    [twitterInfoRequest setAccount:self.twitterAccount];

    [twitterInfoRequest performRequestWithHandler:^(NSData *responseData, NSHTTPURLResponse *urlResponse, NSError *error) {
        dispatch_async(dispatch_get_main_queue(), ^{
            // 2
        });
    }];
}
```

This request will give us a list of user IDs for the user’s friends. To organize that data in an array and fill the table, replace the `// 2` comment with the following code:

```objectivec
if ([urlResponse statusCode] == 429) {
    NSLog(@"Rate limit reached");
    return;
}
if (error) {
    NSLog(@"Error: %@", error.localizedDescription);
    return;
}
if (responseData) {
    NSError *error = nil;
    NSDictionary *TWData = [NSJSONSerialization JSONObjectWithData:responseData options:NSJSONReadingMutableLeaves error:&error];
    NSArray *friendList = [TWData valueForKey:@"ids"];
    [self fillTable:friendList];
}
```

You should get an error saying that we haven’t defined `fillTable`. Now is a great time to define it, so add the following method stub:

```objectivec
- (void) fillTable:(NSArray *) friendList {

}
```

Now, add the following for-loop:

```objectivec
for (NSString *friendID in friendList) {
    SLRequest *twitterInfoRequest = [SLRequest requestForServiceType:SLServiceTypeTwitter requestMethod:SLRequestMethodGET URL:[NSURL URLWithString:@"https://api.twitter.com/1.1/users/lookup.json?"] parameters:[NSDictionary dictionaryWithObjectsAndKeys:[NSString stringWithFormat:@"%@", friendID], @"user_id", nil]];
    [twitterInfoRequest setAccount:self.twitterAccount];
    [twitterInfoRequest performRequestWithHandler:^(NSData *responseData, NSHTTPURLResponse *urlResponse, NSError *error) {
        dispatch_async(dispatch_get_main_queue(), ^{
            // 3
        });
    }];
}
```

For every user followed in the list, we’ll go ahead and make a request to store that user’s info. To store the info, replace the `// 3` comment with:

```objectivec
if ([urlResponse statusCode] == 429) {
    NSLog(@"Rate limit reached");
    return;
}
if (error) {
    NSLog(@"Error: %@", error.localizedDescription);
    return;
}
if (responseData) {
    NSError *error = nil;
    NSDictionary *TWData = [NSJSONSerialization JSONObjectWithData:responseData options:NSJSONReadingMutableLeaves error:&error];

    CFriend *newFriend = [[CFriend alloc] init];
    [newFriend setUserID:friendID];
    [newFriend setName:[[TWData valueForKey:@"name"] objectAtIndex:0]];
    [newFriend setUsername:[[TWData valueForKey:@"screen_name"] objectAtIndex:0]];
    [newFriend setPicUrl:[[TWData valueForKey:@"profile_image_url"] objectAtIndex:0]];

    if (!self.objects) {
        self.objects = [[NSMutableArray alloc] init];
    }
    [self.objects addObject:newFriend];
    NSIndexPath *indexPath = [NSIndexPath indexPathForRow:_objects.count-1 inSection:0];
    [self.tableView insertRowsAtIndexPaths:@[indexPath] withRowAnimation:UITableViewRowAnimationAutomatic];
    [self.tableView reloadData];
}
```

Here, we store a user’s information in an object. I’ve gone ahead and defined a class called CFriend for this tutorial.

Now we’re ready to call `getFriends` in `viewDidLoad`:

```objectivec
- (void)viewDidLoad {
    [super viewDidLoad];
    [self getFriends];
}
```

Alright, now a list of people whom the user follows should appear in the table view.

That’s great and all, but let’s add a bit of flair to the UI. In some iOS applications, like the Music app, you’ll find table views with cells that have subtitles. Let’s do that here. The main text in each table view cell will be the user’s name, and the subtext will be the user’s username on Twitter.

Find the method named `tabelView:cellForRowAtIndexPath:`. You should see a line that says:

```objectivec
UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"Cell" forIndexPath:indexPath];
```

Change that to read as follows:

```objectivec
UITableViewCell *cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleSubtitle reuseIdentifier:@"Cell"];
```

Now we’ll return a cell that can display subtext. Before the return statement, add the following two lines:

```objectivec
[cell.textLabel setFont:[UIFont systemFontOfSize:20]];
cell.detailTextLabel.text = [NSString stringWithFormat:@"@%@", object.username];
```

Bingo\!

![Friends.png](images\c2aefcd-Friends.png)

So far we’ve integrated Twitter into our app. Now let’s add calling.

## 3. Call screen and calling with SinchService

First, go to **DetailViewController.m** and add the following method:

```objectivec
- (void) displayPictureForUrl:(NSString *)urlString {
    NSURL *url = [NSURL URLWithString:urlString];
    NSData *imageData = [NSData dataWithContentsOfURL:url];
    UIImage *img = [UIImage imageWithData:imageData];
    CGSize size = img.size;
    CGRect rectFrame = CGRectMake(self.view.frame.size.width/2 - size.width * 0.75, 90, size.width * 1.5, size.height * 1.5);
    UIImageView* imgv = [[UIImageView alloc] initWithImage:img];
    imgv.frame = rectFrame;

    // make picture into circle
    imgv.layer.cornerRadius = size.width/2;
    imgv.layer.masksToBounds = YES;

    [self.view addSubview:imgv];
}
```

This is a handy way of displaying a profile picture from a URL. Now, locate the method `configureView`. In the if-statement, add the following three lines to display a user’s info:

```objectivec
self.NameLabel.text = self.callFriend.name;
self.UsernameLabel.text = [NSString stringWithFormat:@"@%@", self.callFriend.username];
[self displayPictureForUrl:self.callFriend.picUrl];
```

To display the screen, go back to **MasterViewController.m** and implement this method:

```objectivec
-(void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath
{
    DetailViewController *controller = [self.storyboard instantiateViewControllerWithIdentifier:@"callScreen"];
    CFriend *callingFriend = self.objects[indexPath.row];
    [controller setCallFriend:callingFriend];
    // 4
    [self presentViewController:controller animated:YES completion:nil];
}
```

Now we can implement calling. Before we can make any calls, we need to create and initialize a client that will handle calls.

Go to **AppDelegate.h** and add the following imports:

```objectivec
#import <Sinch/Sinch.h>
#import <SinchService/SinchService.h>
```

Then, add the following properties to the interface:

```objectivec
@property (strong, nonatomic) id<SINService> sinch;
@property (strong, nonatomic) ACAccount *twitterAccount;
```

Next, go to **AppDelegate.m**. Add the **SINServiceDelegate** and **SINCallClientDelegate** protocols to the implementation:

```objectivec
@interface AppDelegate () <SINServiceDelegate, SINCallClientDelegate>
@end
```

Now, add the following code to `application:didFinishLaunchingWithOptions`:

```objectivec
id config = [SinchService configWithApplicationKey:@"application-key"
                                 applicationSecret:@"application-secret"
                                   environmentHost:@"clientapi.sinch.com"];

id<SINService> sinch = [SinchService serviceWithConfig:config];
sinch.delegate = self;
sinch.callClient.delegate = self;

void (^onUserDidLogin)(NSString *) = ^(NSString *userId) {
    [sinch logInUserWithId:userId];
};

self.sinch = sinch;

[[NSNotificationCenter defaultCenter]
 addObserverForName:@"UserDidLoginNotification"
 object:nil
 queue:nil
 usingBlock:^(NSNotification *note) { onUserDidLogin(note.userInfo[@"userId"]); }];
```

Here we are initializing the SINService object and setting up NSNotificationCenter. We need to set up NSNotificationCenter so we can log users into the app using their usernames.

Go to **DetailViewController.h** and add the following import:

```objectivec
#import <Sinch/Sinch.h>
```

Then, add two protocols to make the implementation line look like this:

```objectivec
@interface DetailViewController : UIViewController <SINCallClientDelegate, SINCallDelegate>
```

Add a property that will hold the user’s call:

```objectivec
@property (nonatomic, readwrite, strong) id<SINCall> call;
```

In **DetailViewController.m**, add the following setter method to properly set the SINCall property:

```objectivec
- (void)setCall:(id<SINCall>)call {
    _call = call;
    _call.delegate = self;
}
```

Great\! Now we have a call variable for the call screen, as well as a way to set it. Go back to **AppDelegate.m** and add the following method to handle incoming calls:

```objectivec
- (void)client:(id<SINCallClient>)client didReceiveIncomingCall:(id<SINCall>)call {
    UIViewController *top = self.window.rootViewController;
    SLRequest *twitterInfoRequest = [SLRequest requestForServiceType:SLServiceTypeTwitter requestMethod:SLRequestMethodGET URL:[NSURL URLWithString:@"https://api.twitter.com/1.1/users/lookup.json?"] parameters:[NSDictionary dictionaryWithObjectsAndKeys:[NSString stringWithFormat:@"%@", [call remoteUserId]], @"screen_name", nil]];
    [twitterInfoRequest setAccount:self.twitterAccount];
    [twitterInfoRequest performRequestWithHandler:^(NSData *responseData, NSHTTPURLResponse *urlResponse, NSError *error) {
        dispatch_async(dispatch_get_main_queue(), ^{
            if ([urlResponse statusCode] == 429) {
                NSLog(@"Rate limit reached");
                return;
            }
            if (error) {
                NSLog(@"Error: %@", error.localizedDescription);
                return;
            }
            if (responseData) {
                NSError *error = nil;
                NSDictionary *TWData = [NSJSONSerialization JSONObjectWithData:responseData options:NSJSONReadingMutableLeaves error:&error];

                CFriend *callingFriend = [[CFriend alloc] init];
                [callingFriend setUserID:[[TWData valueForKey:@"id"] objectAtIndex:0]];
                [callingFriend setName:[[TWData valueForKey:@"name"] objectAtIndex:0]];
                [callingFriend setUsername:[call remoteUserId]];
                [callingFriend setPicUrl:[[TWData valueForKey:@"profile_image_url"] objectAtIndex:0]];

                DetailViewController *controller = [top.storyboard instantiateViewControllerWithIdentifier:@"callScreen"];
                [controller setCallFriend:callingFriend];
                [controller setCall:call];
                [self.window.rootViewController presentViewController:controller animated:YES completion:nil];
            }
        });
    }];
}
```

You’ll see that we set the Twitter ACAccount property when this method is called. That property will help us get the caller’s info for the call screen. We still need to set this property and log the user in, so go to **LoginViewController.m** and replace the `// 1` comment with:

```objectivec
[[NSNotificationCenter defaultCenter] postNotificationName:@"UserDidLoginNotification"
                                                                    object:nil
                                                                  userInfo:@{@"userId" : twitterAccount.username}];


                AppDelegate *SharedAppDelegate = (AppDelegate *)[[UIApplication sharedApplication] delegate];
                [SharedAppDelegate setTwitterAccount:twitterAccount];
```

Now, to place a call, go to **MasterViewController.m** and find `tableView:didSelectRowAtIndexPath:`. Replace the `// 4` comment with:

```objectivec
id<SINCall> call = [self.callClient callUserWithId:[callingFriend username]];
[controller setCall:call];
```

You’ll get an error. That’s because we still need to get the Sinch client from the AppDelegate. Add the following method to return the Sinch client’s call client property:

```objectivec
- (id<SINCallClient>)callClient {
    return [[(AppDelegate *)[[UIApplication sharedApplication] delegate] sinch] callClient];
}
```

Now whenever a user is selected, he or she will be called.

Let’s finish off the call screen now. Go to **DetailViewController.m** and find `configureView`. Add the following code to the if-statement:

```objectivec
if ([self.call direction] == SINCallDirectionIncoming) {
    self.AnswerButton.hidden = NO;
    self.StatusLabel.text = @"";
} else {
    self.AnswerButton.hidden = YES;
    self.StatusLabel.text = @"calling...";
}
```

It’s pretty simple to handle the IBActions with Sinch calls. You can just add one line to each method:

```objectivec
- (IBAction)AnswerAction:(id)sender {
    [self.call answer];
}

- (IBAction)HangupAction:(id)sender {
    [self.call hangup];
}
```

Finally, let’s take advantage of some handy delegate methods to change some of the on-screen text:

```objectivec
- (void)callDidEstablish:(id<SINCall>)call {
    self.AnswerButton.hidden = YES;
    [self.HangupButton setTitle:@"Hangup" forState:UIControlStateNormal];
    self.StatusLabel.text = @"";
}

- (void)callDidEnd:(id<SINCall>)call {
    [self dismissViewControllerAnimated:YES completion:nil];
}
```
![Call-out.png](images\573ba24-Call-out.png)

Congrats\! You’ve created a basic app that lets users call people they follow on Twitter. As you can see, it’s simple to integrate Sinch into your app.

One feature you might want to add to this app is Managed Push. ([Learn more about Managed Push with SinchService](doc:voice-ios-push-notifications-callkit)) You can also find more Sinch tutorials on the <span class="title-ref">Sinch tutorial site \<tutorialsite\></span> or on [Sinch’s GitHub](https://github.com/sinch). Of course, you can also find us on Twitter [@SinchDev](https://twitter.com/sinchdev).

