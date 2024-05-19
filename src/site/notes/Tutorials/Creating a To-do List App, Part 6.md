---
{"dg-publish":true,"permalink":"/tutorials/creating-a-to-do-list-app-part-6/","dgHomeLink":true,"dgShowToc":true}
---


# Creating a To-do List App, Part 6

In [[Tutorials/Creating a To-do List App, Part 5\|part five]] of this task, you learned about the MVVM design pattern and how it promotes separation of concerns within an app. You set up a Supabase account and created a database to store the to-do list items. Finally, you saw how Supabase sends data from the database over the World Wide Web.

In this continuation of that tutorial, you will learn how to consume data from a Supabase cloud-hosted database directly within an iOS app.

## Unpause your database

If you completed part 5 of this tutorial series in the prior module here at LCS, it's quite likely that your database, hosted by Supabase, has been paused to conserve server-side resources. This occurs automatically for free-tier projects at Supabase when a database has been inactive for 7 days. 

To get started, first be sure you are [signed in at GitHub](https://github.com/login).

> [!REMINDER]
> 
> It's been a while, but recall that when you created a Supabase account, you [[Tutorials/Creating a To-do List App, Part 5#Create an account\|used your GitHub credentials]].

Now, visit the [sign in page at Supabase](https://supabase.com/dashboard) and you will see a page like this:

![Screenshot 2024-05-18 at 2.26.52 PM.png](/img/user/Media/Screenshot%202024-05-18%20at%202.26.52%E2%80%AFPM.png)

Please choose **Continue with GitHub**.

After a moment, you will see your dashboard at Supabase:

![Screenshot 2024-05-18 at 2.27.48 PM.png](/img/user/Media/Screenshot%202024-05-18%20at%202.27.48%E2%80%AFPM.png)

Notice that your project is likely listed as paused. If you click on your project to open it, you will see this message:

![Screenshot 2024-05-18 at 2.28.52 PM.png](/img/user/Media/Screenshot%202024-05-18%20at%202.28.52%E2%80%AFPM.png)

Just press the green **Restore project** button and then press it again on the confirmation screen:

![Screenshot 2024-05-18 at 2.29.33 PM.png](/img/user/Media/Screenshot%202024-05-18%20at%202.29.33%E2%80%AFPM.png)

You will see this message for a few minutes. Since your database is very small, it won't take long to restore:

![Screenshot 2024-05-18 at 2.29.47 PM.png](/img/user/Media/Screenshot%202024-05-18%20at%202.29.47%E2%80%AFPM.png)

When restoration is complete, you will see a screen like this one:

![Screenshot 2024-05-18 at 2.30.56 PM.png](/img/user/Media/Screenshot%202024-05-18%20at%202.30.56%E2%80%AFPM.png)

Select the **Table Editor** button and then select the `todos` table at left:

![Screenshot 2024-05-18 at 2.31.48 PM.png](/img/user/Media/Screenshot%202024-05-18%20at%202.31.48%E2%80%AFPM.png)

You should then see whatever to-do list items you created in [[Tutorials/Creating a To-do List App, Part 5\|part 5 of this tutorial series]]:

![Screenshot 2024-05-18 at 2.32.47 PM.png](/img/user/Media/Screenshot%202024-05-18%20at%202.32.47%E2%80%AFPM.png)

## Understanding JSON

In part 5 you learned how to [[Tutorials/Creating a To-do List App, Part 5#Fetching table data\|see the items in your database table]] over the web, except you used the `curl` command-line app as your "web browser".

Right-click and [[Tutorials/Creating a To-do List App, Part 5#Fetching table data\|open those instructions again]]. Follow them to refresh your memory so that you see output like this in the terminal:

![Screenshot 2024-05-18 at 2.37.19 PM.png](/img/user/Media/Screenshot%202024-05-18%20at%202.37.19%E2%80%AFPM.png)

So, what is this information that was returned when we made our request to the database?

```json
[{"id":1,"title":"Call Mom","done":false}, 
 {"id":2,"title":"Go for a run","done":false}, 
 {"id":3,"title":"Have a nap","done":false}]
```

The information shown there is simply plain text – however, certain rules are followed so that the information is in a predictable format.

**JavaScript Object Notation** format, or **JSON**, is the set of rules being used to format the information sent to us by the Supabase platform.

In JSON:

- square brackets mark the start and end of an array
- curly brackets mark the start and end of an object
- an object is composed of data
	- data are sent in name/value pairs
		- the name comes before a colon
		- the value comes after a colon

So for this data:

```json
[{"id":1,"title":"Call Mom","done":false}, 
 {"id":2,"title":"Go for a run","done":false}, 
 {"id":3,"title":"Have a nap","done":false}]
```

... there is:

- one array
- three objects are in the array
- each object has three name/value pairs

Our goal is to get that data out of it's plain-text JSON format, and into the Swift data structures we set up in part 5. 

Each JSON object will correspond to an instance of our `TodoItem` structure:

```swift
struct TodoItem: Identifiable {
    let id = UUID()
    var title: String
    var done:  Bool
}
```

The JSON array corresponds to the `todos` array we set up in our view model:

![Screenshot 2024-05-18 at 2.52.28 PM.png](/img/user/Media/Screenshot%202024-05-18%20at%202.52.28%E2%80%AFPM.png)

We are going to use the framework provided by Supabase so that this process of converting data kept in JSON format into Swift data structures is super convenient and automatic.

> [!NOTE]
> 
> Eagle-eyed readers may have noticed that the `id` property being sent to us by Supabase is a simple integer value (`1`, `2`, `3`, and so on) but the `id` property in our `TodoItem` structure is a `UUID` or universally-unique identifier, which looks something like this: `5F944282-A194-4A6C-A1E4-2022FBD8D8A8`
> 
> Don't worry. We'll fix this, and it's easy!

## Adding the Supabase framework

To add the Supabase framework, from the menus, select **File > Add Package Dependences...**:

![Screenshot 2024-05-19 at 8.00.29 AM.png](/img/user/Media/Screenshot%202024-05-19%20at%208.00.29%E2%80%AFAM.png)

Paste the address shown here in the top-right corner:

```
https://github.com/supabase-community/supabase-swift
```

You should see the following:

![Screenshot 2024-05-19 at 8.01.40 AM.png](/img/user/Media/Screenshot%202024-05-19%20at%208.01.40%E2%80%AFAM.png)

Then select **Add Package** and you will see this screen briefly:

![Screenshot 2024-05-19 at 8.02.17 AM.png](/img/user/Media/Screenshot%202024-05-19%20at%208.02.17%E2%80%AFAM.png)

On the screen that appears, expand the window if needed, then for each package product shown, change the option for the **Add to Target** column to read **TodoList** (this is the app or target name of your project):

![Screenshot 2024-05-19 at 8.04.10 AM.png](/img/user/Media/Screenshot%202024-05-19%20at%208.04.10%E2%80%AFAM.png)

Finally, press the **Add Package** button:

![Screenshot 2024-05-19 at 8.04.49 AM.png](/img/user/Media/Screenshot%202024-05-19%20at%208.04.49%E2%80%AFAM.png)

If all goes well you will see the frameworks that have been added in the lower left corner of the Xcode window: 

![Screenshot 2024-05-19 at 8.10.14 AM.png](/img/user/Media/Screenshot%202024-05-19%20at%208.10.14%E2%80%AFAM.png)

## Modifying the view model

A tremendous benefit of separating concerns within an app is that when you make changes to how data is persisted, the view layer never needs to know about it, and so no source code in the view layer needs to be modified.

Here is the current overall architecture of our to-do list app:

```mermaid
flowchart LR

id1["<b>Model</b>\nDescribes data"] --> vm
subgraph vm["<b>View Model</b>"]
	direction TB
	id2["Manages the\nstate of data"]
	id2-.->id4
end
vm --> id3["<b>View(s)</b>\nPresent data"]
id3 --> vm
id4["Data is kept\n<em>in memory</em>\n<div class="time-to-get-ill">
  <style>
    .icon {
      width: 1em;
      height: 1em;
      vertical-align: -0.125em;
    }
  </style>
  What time is it...
  <svg xmlns="http://www.w3.org/2000/svg" class="icon" aria-hidden="true" focusable="false" viewBox="0 0 512 512">
    <path
      fill="currentColor"
      d="M256 8C119 8 8 119 8 256s111 248 248 248 248-111 248-248S393 8 256 8zm0 448c-110.5 0-200-89.5-200-200S145.5 56 256 56s200 89.5 200 200-89.5 200-200 200zm61.8-104.4l-84.9-61.7c-3.1-2.3-4.9-5.9-4.9-9.7V116c0-6.6 5.4-12 12-12h32c6.6 0 12 5.4 12 12v141.7l66.8 48.6c5.4 3.9 6.5 11.4 2.6 16.8L334.6 349c-3.9 5.3-11.4 6.5-16.8 2.6z"
    />
  </svg>
</div>"]
```


