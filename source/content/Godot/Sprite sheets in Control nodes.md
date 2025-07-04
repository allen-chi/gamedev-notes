# Use case
I found myself wanting to use sprites in a **Control** node for a UI element but discovered that the **Sprite2D** node doesn't respect the **Control** node limitations. I wanted to display a small menu item that would show what crafting items had already been inserted into a forge.


# Solution
I came across this [YouTube video](https://www.youtube.com/watch?v=mwIkj3FfatQ&t=35s) that goes over how to use a **TextureRect** and **AtlasTexture** to accomplish analogous behaviour.

1. Create a **TextureRect** as a child of your **Control** node
2. Create an **AtlasTexture**
3. Add your sprite sheet to the **AtlasTexture**
4. Under `Region`, set `w` and `h` to the size of each sprite frame; set the `x` and `y` values to multiples of each frame

The above steps should allow you to select a particular frame from your sprite sheet for display.

# Extending the solution
I took the concept slightly further to increase usability by adding a script that would allow for the **TextureRect** to behave more similarly to **Sprite2D** nodes. This can be accomplished by exporting nodes and doing some math so that the `region` could be set more easily.

```gdscript
@export_group("Spritesheet coordinate")
@export_range(0, 18, 1) var h_frames: int = 18 : set = update_h_var
@export_range(0, 18, 1) var v_frames: int = 0 : set = update_v_var
@export var frame_size: int = 32
```

Then, we replicate the behaviour with a bit of math.
```gdscript
# Put this into _ready()
func set_texture_rect() -> void:
    texture.region = Rect2(v_frames * 32, h_frames * 32, 32, 32)
```

Ultimately, the script should allow the **TextureRect** to behave in a similar manner to  **Sprite2D** in terms of picking a frame out of a sprite sheet.

The final implementation was a `@tool` script that could be easily configured in the editor.

```gdscript
@tool 
@export_group("Spritesheet coordinate")
@export_range(0, 18, 1) var h_frames: int = 18 : set = update_h_var
@export_range(0, 18, 1) var v_frames: int = 0 : set = update_v_var
@export var frame_size: int = 32


func _ready() -%3E void:
	texture = texture.duplicate(true)
	set_texture_rect()


func set_texture_rect() -> void:
	texture.region = Rect2(v_frames * frame_size, h_frames * frame_size, frame_size, frame_size)


func update_v_var(value):
	v_frames = value
	set_texture_rect()


func update_h_var(value):
	h_frames = value
	set_texture_rect()
```

# Last words
I hope you've found this helpful. I'm sure there's probably a better way to do this, but I'm glad I was able to discover a solution to make my life a little bit easier given the solution at hand. 