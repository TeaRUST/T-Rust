# Context

Context (in the state machine) records a list of operations that execute [commands](commands.md), which **should** be applied to the [state](state.md) if committed. But before the final commit, the state itself is not changed, and only to-be-changed operations are stored in the context.

The goal of context is an atomic commit. All the operations are either committed successfully or not committed at all. 

During the execution of [commands](commands.md) in the [state_machine_actor](state_machine_actor.md)s, all operations will be inspected to make sure they would be successfully commited. This is very important. All operations inside context need to be guaranteed successful. If any operation would fail, this command is aborted and the context will be dropped. In that case, nothing will be committed, and the state will not change at all.

In order to make sure that all operations inside a context are guaranteed to be committed successfully, we require commands to be executed in a single thread. All execution functions are **pure functional functions**. That means all required parameters have been included in the function parameters, and there are no additional external inputs or conditions. The function result is deterministic. **Deterministic** is an important requirement in the [state_machine_actor](state_machine_actor.md) handler functions. If determinism is not guaranteed, the [state machine](state_machine.md) in a different [state_machine_replica](state_machine_replica.md) may not sync. In other words, the state machine **cannot reach consensus** and will cause the [state](state.md) to be out of sync.
