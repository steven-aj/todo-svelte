<script>
	// @ts-nocheck

	import { createEventDispatcher } from 'svelte';

	export let title;
	export let tasks;
   export let clearable;

	let dispatch = createEventDispatcher();

	function emitTaskStatus(task, status) {
		dispatch('toggle', { task, completed: status });
	}

   function emitClear() {
      dispatch('clear', {tasks});
   }
</script>

{#if tasks}
   <nav>
      <ul>
         <li>
            {title ? title : ''}
         </li>
      </ul>
      <ul>
         <li hidden={!clearable}>
            <button class="secondary outline" on:click={emitClear}>Clear</button>
         </li>
      </ul>
   </nav>
	<section class="tasks">
		<ul>
			{#each tasks as task}
				<label 
            class={task.completed ? 'completed' : ''} 
            for={task.id} checked={task.completed} 
            on:keydown={e => emitTaskStatus(task, !task.completed)}
            on:click={e => emitTaskStatus(task, !task.completed)}>
					<input
						type="checkbox"
						name={task.id}
						value={task.completed}
						on:change={(e) => emitTaskStatus(task, !task.completed)}
						bind:checked={task.completed}
					/>
					{task.name}
				</label>
			{/each}
		</ul>
	</section>
{/if}

<style>
	h2 {
		color: var(--secondary);
      margin-top: 0;
      margin-bottom: 1rem;
	}

   .tasks ul {
      display: flex;
      flex-direction: column;
      gap: 0.5em;
   }

   .tasks label {
      padding-bottom: 1em;
      border-bottom: thin dashed var(--secondary);
      animation: slide-left 0.5s;
   }

	.completed {
		color: var(--secondary);
		text-decoration: line-through;
	}
</style>
