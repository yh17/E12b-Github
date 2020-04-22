# E12b-Github

This repository contains a very simple Godot project, but the exercise centers on tools (available to you through Github) for collaborating with others on a project like this one. 

First, you will need to review [Understanding the GitHub Flow](https://guides.github.com/introduction/flow/). This will provide a basic overview of the process of branching, merging, and pull requests.

Then, fork this repository. In GitHub Desktop, clone it to your computer.

Before you leave GitHub Desktop, go to the Branch menu and select New Branch. Name this branch Development (in general, you can name branches whatever you want). The current Branch label in the GitHub Desktop window should now say Development.

Open the project.godot file in Godot. Right-click on the Player node, and Attach Script. Save the script as res://Player.gd, and then paste the following into the resulting file:

```
extends KinematicBody

onready var Camera = $Camera

var velocity = Vector3()
var speed = 10
var max_speed = 100
var turn_sensitivity = 2
var rot_sensitivity = 20

var turning = 0
var pitching = 0
var decay = 0.5

var rot_x = 0.0
var rot_y = 0.0
var rot_z = 0.0

func _ready():
	pass

func get_input(delta):
	if Input.is_action_pressed("Up"):
		rot_x += rot_sensitivity * delta
	if Input.is_action_pressed("Down"):
		rot_x -= rot_sensitivity * delta
	if Input.is_action_pressed("Left"):
		rot_y += rot_sensitivity * delta
		turning += turn_sensitivity
	if Input.is_action_pressed("Right"):
		rot_y -= rot_sensitivity * delta
		turning -= turn_sensitivity
	turning = clamp(turning, -30.0, 30.0)


func _physics_process(delta):
	get_input(delta)

	turning = sign(turning) * (abs(turning) - decay)
	if abs(turning) < decay: turning = 0
	
	rotation_degrees.x = rot_x
	rotation_degrees.y = rot_y
	rotation_degrees.z = turning
	
	translate(-$Reference.transform.basis.z * speed * delta)
```

Save the file, quit Godot, and return to Github Desktop. Commit your changes to the Development branch, but don't push them yet.

If you click on the Current Branch button, you can change it back to Master. If you look in the folder on your computer where your project is saved, you should see that the Player.gd file is no longer there. It hasn't been deleted, it is just associated with a different branch of your code which is not currently selected.

Click the Current Branch button and select Development again. Publish the branch (where the push button normally is) to Github.

Go to github.com, and  just above the file view, you should see an indicator that there are 2 branches. If you select that, you should see the master and the Development branch listed; next to the Development branch, you should see a "New pull request" button. Select that now.

The pull request page should say that this branch is able to merge, and you should see a form with your commit summary (as well as any comments you left). Leave any addition comments you would like to, and press "Create pull request". Confirm the request.

This will take you to the Pull Request page, which merges the code from the Development branch in to the master branch. There are no conflicts, so it has merged and closed the pull request.

Now, go back to Github Desktop and select the master branch. In the Branch menu, create a New Branch. Call this branch Development2.

Open the project.godot file in Godot. You will notice that the player node no longer has a script attached. Right-click and Attach Script. Save the script as res://Player.gd, and then paste the following into the resulting file:

```
extends KinematicBody

var velocity = Vector3()
var gravity = -9.8
var speed = 8
var mouse_sensitivity = 0.002
var mouse_range = 1.2
var jump = 3
var jumping = false

var has_key = false
var has_crate = false

var health = 100

func _ready():
	Input.set_mouse_mode(Input.MOUSE_MODE_CAPTURED)

func get_input():
	var input_dir = Vector3()
	if Input.is_action_pressed("forward"):
		input_dir += -Camera.global_transform.basis.z
	if Input.is_action_pressed("back"):
		input_dir += Camera.global_transform.basis.z
	if Input.is_action_pressed("left"):
		input_dir += -Camera.global_transform.basis.x
	if Input.is_action_pressed("right"):
		input_dir += Camera.global_transform.basis.x
	if Input.is_action_pressed("jump"):
		jumping = true
	input_dir = input_dir.normalized()
	return input_dir


func _physics_process(delta):
	if health <= 0:
		return
	velocity.y += gravity * delta
	var desired_velocity = get_input() * speed
	
	velocity.x = desired_velocity.x
	velocity.z = desired_velocity.z
	if jumping and is_on_floor():
		velocity.y = jump
	jumping = false
	velocity = move_and_slide(velocity, Vector3.UP, true)

func _unhandled_input(event):
	if health <= 0:
		return
	if event is InputEventMouseMotion and Input.get_mouse_mode() == Input.MOUSE_MODE_CAPTURED:
		rotate_x(-event.relative.y * mouse_sensitivity)
		rotate_y(-event.relative.x * mouse_sensitivity)
```

Save the file, and quit Godot. In Github Desktop, commit your changes, and publish them back to Github.

Go to github.com, and go to the repository page. Just above the file view, you should see an indicator that there are now 3 branches. On the branch page, next to the Development2 branch, you should see a "New pull request" button. Select that now.

The pull request page should say that this branch can't automatically merge, but you should still see a form with your commit summary (as well as any comments you left). Leave any addition comments you would like to, and press "Create pull request".

This will take you to the Pull Request page, in which you will have the opportunity to merge the code from the Development branch in to the master branch. You should see that "This branch has conflicts that must be resolved". Press the "Resolve Conflicts" button.

You will be brought to an edit page, in which you will see some lines marked with 

```
<<<<<< Development2
followed by 
=======
followed by 
>>>>>> master
```

These are the areas you will need to edit. Remove the Development2 changes (as well as the <<<<< and ===== lines), and press the "Mark as Resolved" button. Then press the "Commit merge" button.

You should now be back on the Pull Request page and see that This branch has no conflicts with the base branch. Press the "Merge pull request" button. Confirm the merge.

This is one possible model for collaborating on a single repository. In a collaborative project, multiple people can Clone a single repository. If each of them is working in their own branch, then the repository owner can assume the responsibility of merging those changes back to the main branch. 

When you have completed the exercise, update the LICENSE and README.md, and turn in the URL of your repository on Canvas.
