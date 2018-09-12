IocageEvent is a core structure used all across iocage. Whenever iocage faces async tasks, it communicates by yielding IocageEvent instances.

The Python 3 language feature Generators (Iterators) allows to communicate state during the runtime of a method, so that every task gives agile feedback on its progress.

Because Python Generators block until the yielded variable was consumed, some classes (for example `Jail`) provide a user-friendly abstraction of the internal Generator-class. The folliing two snippets will have the same effect of starting a Jail:

```python
iocage.Jail("myjail").start()
```

```python
jail = iocage.Jail.JailGenerator("myjail")
for event in jail.start():
	continue
```

### IocageEvent State

Generator methods yield each event twice - once when the event begins and another time when it was finished. While the `pending` attribute on each event reflects whether it is currently in progress. Events with the `done` attribute set to `True` are no longer pending, but may be exited with an `error` or have been `skipped`.

| IocageEvent Attribute | Description                                            |
|-----------------------|--------------------------------------------------------|
| pending               | `True` when the event is currently occuring            |
| done                  | `True` when the event terminated                       |
| error                 | Optional Exception of failed done events.              |
| skipped               | `True` when a done event had no effect and was skipped |

### Event Stacking

It is possible that an event is still pending while other events are handled. When another event is started while another event is not finished yet, it becomes a nested child event.

From an event consumers perspective a typical stack of events provided by a generator looks like

- Event A: begin
	- Event B: begin
	- Event B: end
	- Event C: begin
	- Event C: end
- Event A: end

The start command of a jail is a good example to demonstrate the consumption of events. This time the `iocage.Jail.JailGenerator` class is used directly instead of the syncronous wrapper `iocage.Jail.Jail`.

```python
jail = iocage.Jail.JailGenerator("myjail")
for event in jail.start():
	if event.pending is True:
		print(f"{event.type} is pending")
	else:
		if event.skipped is True:
			status = "skipped"
		elif event.error is not None:
			status = "failed"
		else:
			status = "done"
		print(f"{event.type} is {status}")
   print(event.type, event.pending)
```

### Rollback Steps on failed events

When processing stacked tasks it is important to never leave the system in a intermediate state. Whenever an event can cause such persistent changes, a rollback method is attached to the event. This method is called in case of failure of a sibling or child event (in LIFO order), so that changes can be reverted.

This abstract example shows how such a rollback step is implemented by rolling back a ZFS snapshot after a failed event:

```python
import iocage
import libzfs

zfs = libzfs.ZFS()

def fail_doing_something(dataset_name):
	snapshot_name = f"{dataset_name}@demo-snapshot"
	dataset = zfs.get_dataset(dataset_name)
	dataset.snapshot(snapshot_name)
	snapshot = zfs.get_snapshot(snapshot_name)

	def _rollback_snapshot():
		snapshot.rollback()

	myevent = iocage.events.IocageEvent()
	myevent.add_rollback_step(_rollback_snapshot);
	yield myevent.begin()
	# ...
	yield myevent.fail("Some error occurred")

fail_doing_something("zroot/my_dataset")
```