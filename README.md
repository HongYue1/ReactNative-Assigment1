# Report

## Expo Compiler Link: https://snack.expo.dev/@skazka/todo-list


**Project Report: React Native To-Do List Application**

**Introduction:**

I set out to build a simple yet functional to-do list application using React Native. My primary goal was to create a user-friendly interface with core features like adding, deleting, and editing tasks. I also wanted to explore some interaction nuances, like using a long-press gesture to trigger edit mode. This report details my implementation and the reasoning behind my design choices.

**Component Structure and State Management:**

The entire application logic resides within the `App` functional component. I chose to use functional components and React Hooks (`useState`, `useRef`) for managing the application's state and side effects. Here's a breakdown:

- **`useState` for Data:**

  - `goal`: Holds the text currently entered in the input field for new goals.
  - `goalsList`: An array of goal objects. Each object has an `id` (a unique string) and a `text` property (the goal's description). This array drives the `FlatList`.
  - `editingId`: Stores the `id` of the goal currently being edited. If no goal is being edited, this is `null`.
  - `editText`: Holds the text of the goal while it's being edited.

- **`useRef` for Long Press:**
  - `longPressTimeout`: This is a reference to a timer. I use it to detect long presses on list items. It's crucial for managing the timing to differentiate between a tap (delete) and a long press (edit).

**Core Functionality - Adding, Deleting, and Editing Goals:**

1.  **Adding Goals (`handleAddGoal`):**

    - When the "Add Goal" button is pressed, this function is triggered.
    - I first check if the input field (`goal`) is empty after trimming whitespace. If it is, I display an alert to the user, prompting them to enter something. This prevents adding empty goals.
    - If the input is valid, I update the `goalsList` using the spread operator (`...prevGoalsList`) to create a new array. This is important for immutability in React. The new goal object gets a unique ID (generated using `Math.random().toString()`) and the text from the input field.
    - Finally, I clear the input field (`setGoal('')`) to prepare for the next entry.

2.  **Deleting Goals (`handleDeleteGoal`):**

    - This function is called when a user taps on a goal item (short press).
    - I implemented a confirmation dialog using `Alert.alert`. This gives the user a chance to cancel the deletion, preventing accidental data loss.
    - If the user confirms, I update `goalsList` by filtering out the goal with the matching `id`. The `filter` method creates a new array, again respecting immutability.

3.  **Editing Goals (Long Press, `handlePressIn`, `handlePressOut`, `saveEdit`, `cancelEdit`):**

    - **Long Press Detection:**

      - `handlePressIn`: When the user presses down on a list item, I start a timer (`setTimeout`) stored in `longPressTimeout.current`. If the press lasts for 500ms (my chosen threshold), I consider it a long press.
      - `handlePressOut`: If the user releases the press _before_ the 500ms timeout, I clear the timeout (`clearTimeout`). This prevents the edit mode from triggering on a short press.
      - If the timeout completes, I set `editingId` to the `id` of the pressed item and `editText` to the item's current text. This puts the app into "edit mode."
      - The reason I didn't immeditally delete when pressing, but do it only on `handlePress` it's because this function is only called if the timer is cleared, thus making the delete function trigger only when a short press is performed.

    - **Saving Edits (`saveEdit`):**

      - When the "Save" button (in the edit view) is pressed, this function is called.
      - I check if the `editText` is empty after trimming. If so, I show an alert, preventing the user from saving an empty goal.
      - If the edited text is valid, I update `goalsList` using `map`. I iterate through the existing goals, and if a goal's `id` matches `editingId`, I replace its `text` with the new `editText`.
      - I then exit edit mode by setting `editingId` to `null` and clearing `editText`.

    - **Canceling Edits (`cancelEdit`):**
      - This function simply exits edit mode without saving any changes. It resets `editingId` and `editText`.

**Rendering the List (`renderItem`):**

- The `renderItem` function is used by the `FlatList` to render each goal.
- **Conditional Rendering:** I use a conditional check (`item.id === editingId`) to determine whether to display the normal list item view or the edit view.
  - **Normal View:** A `TouchableOpacity` that wraps a `View` containing the goal's text. The `TouchableOpacity` handles the press, press-in, and press-out events for delete and edit functionality.
  - **Edit View:** A `View` containing a `TextInput` (for editing the text), and "Save" and "Cancel" buttons.

**Styling (Stylesheet):**

- I created a `styles` object using `StyleSheet.create` to define the appearance of the components.
- I used a `colors` object to store color values, making it easier to maintain a consistent color scheme. I chose a dark theme with blue and lavender accents.
- The styles cover aspects like:
  - Overall container layout (`container`)
  - Title text (`title`)
  - Input field and button styling (`inputContainer`, `input`, `button`, `buttonText`)
  - List item appearance (`listItem`, `listItemText`)
  - Edit mode specific styles (`editContainer`, `editInput`, `editButtonsContainer`, `editButton`, `cancelButton`, `editButtonText`)
