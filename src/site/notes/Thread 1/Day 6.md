---
{"dg-publish":true,"permalink":"/thread-1/day-6/","dgHomeLink":true}
---

### Thread 1, Day 6 - Thursday, October 26, 2023
#### Agenda
1. Setup: [SF Symbols App](https://developer.apple.com/sf-symbols/)
	- Not all software developers have graphic design skills.
	- Platform owners like Apple want their users to have a consistent experience when using apps written by different people.
	- One of the ways Apple helps their developers provide that consistent look and feel is by offering a wide variety of built-in images that can be used in apps.
	- Please [download and install](https://devimages-cdn.apple.com/design/resources/download/SF-Symbols-5.dmg) the SF Symbols app now. 
	  > [!TIP]
	  > To use an SF Symbol within a SwiftUI layout, try the following code:
	  > ```swift
	  >   Image(systemName: "bolt")
	  >       .resizable()
	  >       .scaledToFit()
	  >       .frame(width: 20)
	  > ```
1. Exercise: Create More Wireframe Layouts
	- Select at least five layouts [from these examples](https://russellgordon.ca/lcs/2023-24/ics3u/wireframes.pdf).
	- Consult SwiftUI Views Mastery as a reference.
	- Use the [playground we set up yesterday](https://russellgordon.ca/lcs/2023-24/ics3u/Blueprints.playground.zip) to make your wireframes.
1. Exercise: Create a "Bento-Box" Design
	- Marketing teams must be able to convey new features in a concise manner to an audience.
	- In recent years, Apple has used "Bento-Box" graphics to do so.
	- Try to [[Media/Bento Box Example\|reproduce this image]] using SwiftUI, or use it as inspiration to design a bento-box themed view related to something you care about.
	- Use SwiftUI Views Mastery as a reference.
	  > [!HINT]
	  > Some keywords to look for examples of:
	  > - foregroundStyle
	  > - cornerRadius
	  > - gradient
	  > - Image
	  >   
	  > **Also:** the keyboard shortcut `Option-Shift-K` produces the Apple logo.

###### Things to do before our next class
- [ ] Complete the exercise on layout as described above, then write about what you learned in a portfolio entry on [Notion](https://notion.so).