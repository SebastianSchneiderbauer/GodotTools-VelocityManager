# VelocityManager

Two scripts for Godot 4. Add named forces to a manager, get back one `Vector3` per frame.

```gdscript
var manager := VelocityManager.new()

func _ready() -> void:
	manager.addConstantVelocity(Vector3.DOWN * 9.8, "gravity")

func _physics_process(delta: float) -> void:
	var dir := Input.get_vector("left", "right", "forward", "back")
	manager.addConstantVelocity(Vector3(dir.x, 0, dir.y) * 8.0, "input")
	velocity = manager.getTotalVelocity(delta)
	move_and_slide()

func take_hit(direction: Vector3, force: float) -> void:
	manager.addCurveVelocity(direction * force, preload("res://curves/knockback.tres"), 0.6, "knockback")
```

---

## VelocityManager

```gdscript
# Add
addConstantVelocity(velocity: Vector3, id: String, duration: float = INF)
addCurveVelocity(velocity: Vector3, fallOff: Curve, duration: float, id: String)

# Read
getTotalVelocity(delta: float) -> Vector3   # call once per _physics_process, decreases decreasing Velocities
getVelocity(id: String) -> Velocity         # all other read methods do not decrease Velocities
getVelocityVector(id: String) -> Vector3
hasVelocity(id: String) -> bool
getAllVelocities() -> Dictionary

# Modify
updateVelocity(id: String, updated)         # accepts Velocity | Vector3 | Curve
changeVelocityMultiplier(id: String, value: float) # usefull for modifying gravity without resetting the value directly

# Remove
killVelocity(id: String)
killVelocitySlow(id: String, curve: Curve, duration: float = 1.0) # kills a Velocity over a given time Period, calling this multiple times for the same velocity causes the slowdown to glitch and take longer

# Ignore
addIgnored(input)                           # String or Array[String]
removeIgnored(input: String)
removeAllIgnored()
```
Velocities reaching 0 auto delete themself.

---

## Velocity types

| Type | Behaviour |
|---|---|
| Constant | Holds full strength until `duration` expires, then is removed. |
| Curve | `_direction` is recomputed each frame as `curve.sample(elapsed / duration) * originalDirection`. |

---
