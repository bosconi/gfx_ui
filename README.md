# gfx_ui
A minimal geometry editing UI based on [IMGUI] (https://github.com/ocornut/imgui). I started this project out of frustration after rewriting yet another retained mode geometry matipulation UI for a personal project.

Currently handles these features:

*	Basic point and handle dragging
*	Manipulating affine transforms
*  Rect selection
*  Icon toolbars

![alt tag](https://raw.githubusercontent.com/colormotor/gfx_ui/master/shot.jpg)

## Basic Usage
### Initialization
Call ```ui::init()``` **after** the main IMGUI font has been loaded and **before** the texture atlas has been created (refer to the IMGUI instructions for more details), for example:

```
ImGuiIO& io = ImGui::GetIO();
io.Fonts->AddFontDefault();
ui::init(); // Init here
...
unsigned char* pixels;
int width, height;
io.Fonts->GetTexDataAsAlpha8(&pixels, &width, &height);
...    
```
### UI
The widget calls are slightly different from the usual IMGUI calls:

* Widget IDs are specified as an *index* rather than a string. This is convenient when creating many widgets, for example when editing a poly-line in a loop. Eg:

	```
	ui::begin("Some widgets");
	for(int i=0; i<n; i++){
		v[i] = ui::dragger(i, v[i]);
		angle[i] = ui::handle(i, angle[i], v[i], 100.);
	}
	ui::end()
	```
	
	where `i` is also used as an ID.
	Note that different widgets can be used with the 	same index, because internally IDs will be generated by 	prefixing a widget dependent string.
* Manipulated values are not modified by the widget, but rather are passed by value, and the updated result is returned by the function. This is done with a scripting interface in mind, that would not easily allow the typical IMGUI syntax. To check if the value has been modified by a function use the `ui::modified()` function. 


The geometric manipulation widgets should be called between a `ui::begin` and `ui::end` block. In practice, this actually creates an IMGUI window that covers the whole application window.

### Toolbars
The library contains a function to create a toolbar

```
state = ui::toolbar("a toolbar","ab",state);
```
which can be used to switch between "editing states". 
The icons of the toolbar are created as a TrueType font. By default the code contains and loads the binary data for a number of basic Icons (e.g selection, "pencil", load, save, etc... ). And each Icon is specified in a string, as the corresponding character. For example the previous example crates an arrow icon ('a') and a pencil icon ('b'). A nice tool for creating custom icon fonts (and that I used to make the embedded font) is the [Fontello](http://fontello.com) website.
The toolbar does not necessarily need to called between the `ui::begin`,`ui::end`.

### Configuration
Similarly to ImGui, you can define custom constructors and cast operators for the `ui::Rect` and `ui::Trans2d` structs, which are used for selection and affine transforms respectively.

### UI Focus
Currently, because gfx_ui is using a "full screen" window to handle interaction, code that uses IMGUI's `io.WantCaptureMouse` should use `ui::hasFocus()` instead. 
## Demo
The code comes with a demo (currently tested only on Mac). To run the demo just place the repository folder at the same directory level as the IMGUI examples, cd to the folder, and then

```
make;
./opengl_example
```

Otherwise, you can always call ```ui::demo()``` in your IMGUI code after properly initializing the lib. Looking at the code in this function gives a better overview of the supported functionality.

