# Learning Jetpack Compose

### 1. When is a Composable Function Invoked?

A composable function is invoked in two scenarios:

- **During Initial Composition**: This happens when the UI is first built.
  
- **During Recomposition**: This occurs when a state that the function depends on changes, triggering the function to re-run.

During recomposition, the function is called again, but Jetpack Compose efficiently skips over parts of the UI that don’t depend on the changed state, updating only the necessary elements.
