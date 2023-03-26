<script>
   import { view } from "$data/state/store";
   import Fa from "svelte-fa";
   import { faPlus } from "@fortawesome/free-solid-svg-icons";
   import Task from "$data/database/Task";
   import TaskList from "../components/interface/TaskList.svelte";

   $: view;

   let tasks = Task.queryTasks();
   let completed = Task.queryCompleted();

   function addTask({ task }) {
      if (!task.value) return false;
      Task.todos.add({ name: task.value, completed: false });
      $view.input = false;
      document.getElementById("taskName").value = "";
   }

   function toggleComplete(task, completed) {
      Task.todos.update(task.id, { completed });
   }

   async function clearTasks(list) {
      let message = `Really delete ${list.length} completed ${
         list.length > 1 ? "tasks" : "task"
      }?`;
      if (await confirm(message)) {
         list.forEach((task) => {
            Task.todos.delete(task.id);
         });
      }
   }

   function focus(input) {
      input.focus();
   }
</script>

<form class="input" on:submit|preventDefault={(e) => addTask(e.target)}>
   <nav>
      <input
         id="taskName"
         type="text"
         name="task"
         placeholder="Enter a task name"
         use:focus
      />
      <button type="submit" hidden={true}><Fa icon={faPlus} /></button>
   </nav>
</form>

<TaskList
   title="To-do"
   tasks={$tasks}
   clearable={false}
   on:toggle={({ detail: { task, completed } }) =>
      toggleComplete(task, completed)}
/>

<TaskList
   title="Completed Tasks"
   tasks={$completed}
   clearable={true}
   on:clear={({ detail: { tasks } }) => clearTasks(tasks)}
   on:toggle={({ detail: { task, completed } }) =>
      toggleComplete(task, completed)}
/>

<style>
   .input {
		position: sticky;
		bottom: 0;
      margin-top: 1rem;
	}
</style>
