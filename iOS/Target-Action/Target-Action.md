The Target-Action Mechanism
The target-action mechanism simplifies the code that you write to use controls in your app. Instead of writing code to track touch events, you write action methods to respond to control-specific events. For example, you might write an action method that responds to changes in the value of a slider. The control handles all the work of tracking incoming touch events and determining when to call your methods.

When adding an action method to a control, you specify both the action method and an object that defines that method to the addTarget:action:forControlEvents: method. (You can also configure the target and action of a control in Interface Builder.) The target object can be any object, but it is typically the view controller’s root view that contains the control. If you specify nil for the target object, the control searches the responder chain for an object that defines the specified action method.

The signature of an action method takes one of three forms. The sender parameter corresponds to the control that calls the action method, and the event parameter corresponds to the UIEvent object that triggered the control-related event.

Listing 1 Action method signatures
```
- (IBAction)doSomething;
- (IBAction)doSomething:(id)sender;
- (IBAction)doSomething:(id)sender forEvent:(UIEvent*)event;
```
The system calls action methods when the user interacts with the control in specific ways. The UIControlEvents type defines the types of user interactions that a control can report and those interactions mostly correlate to specific touch events within the control. When configuring a control, you must specify which events trigger the calling of your method. For a button control, you might use the UIControlEventTouchDown or UIControlEventTouchUpInside event to trigger calls to your action method. For a slider, you might care only about changes to the slider’s value, so you might choose to attach your action method to UIControlEventValueChanged events.

When a control-specific event occurs, the control calls any associated action methods immediately. The current UIApplication object dispatches action methods and finds an appropriate object to handle the message, following the responder chain, if necessary. For more information about responders and the responder chain, see Event Handling Guide for UIKit Apps.

Interface Builder Attributes
Table 1 lists the attributes for instances of the UIControl class.

Table 1 Control attributes
Attribute

Description

Alignment

The horizontal and vertical alignment of a control’s content. For controls that contain text or images, such as buttons and text fields, use these attributes to configure the position of that content within the control’s bounds.

These alignment options apply to the content of a control and not to the control itself. For information about how to align controls with respect to other controls and views, see Auto Layout Guide.

Content

The initial state of the control. Use the checkboxes to configure whether the control is in an enabled, selected, or highlighted state initially.

