<script>
   // @ts-nocheck
   import { ask } from "@tauri-apps/api/dialog";
   import { view } from "$data/state/store";
   import Task from "$data/database/Task";
   import TaskInput from "$lib/interface/TaskInput.svelte";
   import TaskList from "$lib/interface/TaskList.svelte";

   $: view;
   
   let tasks = Task.queryTasks();
   let completed = Task.queryCompleted();

   function addTask({ task }) {
      if (!task.value) return false;
      Task.todos.add({
         name: task.value,
         list: $view.list,
         completed: false,
      });
   }

   function toggleComplete(task, completed) {
      Task.todos.update(task.id, { completed });
   }

   async function clearTasks(list) {
      let message = `Really delete ${list.length} completed ${
         list.length > 1 ? "tasks" : "task"
      }?`;
      if (await ask(message, { title: "Clear Tasks", type: "warning" })) {
         list.forEach((task) => {
            Task.todos.delete(task.id);
         });
      }
   }
</script>

<TaskInput
   placeholder="Enter a task name"
   disabled={!$view.list}
   on:create={({ detail }) => addTask(detail)}
/>

<TaskList
   title={`Incomplete ${$view.list} items`}
   tasks={$tasks}
   clearable={false}
   on:toggle={({ detail: { task, completed } }) =>
      toggleComplete(task, completed)}
/>

<TaskList
   title={`Completed ${view.list} items`}
   tasks={$completed}
   clearable={true}
   on:clear={({ detail: { tasks } }) => clearTasks(tasks)}
   on:toggle={({ detail: { task, completed } }) =>
      toggleComplete(task, completed)}
/>

<style>
   details {
      margin-top: 1rem;
   }
</style>
