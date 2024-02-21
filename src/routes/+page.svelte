<script lang="ts">
  import {Container} from '$lib/components';
  import { onMount } from 'svelte';

  type Dog = {
    name: string,
    status: string
  }

  let dogs: Dog[] = [];
  $: newPatient = '';

  onMount(() => {
    if (!localStorage.getItem('dogs')) {
      localStorage.setItem('dogs', '[]');
    }
    dogs = getList();
  });

  function getList() {
    let stored: any = localStorage.getItem('dogs');
    return JSON.parse(stored);
  }

  function addItem(name: string = '') {
    let status = 'listed';
    

    if (name.trim() == '') {
      alert('Please enter a name');
      return;
    }

    if (dogs.some(dog => dog.name === name.trim())) {
      alert('Dog name exists');
      return;
    } else {
      
    }

    dogs = [...dogs, { name: name.trim(), status }];
    localStorage.setItem('dogs', JSON.stringify(dogs));
    dogs = getList();
    newPatient = '';

  }

  function updateStatus(name: string) {
		// Find from array where property name = name
		let index = dogs.findIndex(dog => dog.name === name);
    let status = dogs[index].status;

		if (!confirm(`Update dog patient ${name}'s status?`)) {
			return;
		}
		
    switch (status) {
      case 'listed':
        dogs[index].status = dogs[index].status = 'examining';
        break;
        
      default:
        dogs[index].status = dogs[index].status = 'finally_back_to_hooman';
        break;
    }
    
    localStorage.setItem('dogs', JSON.stringify(dogs));
    dogs = getList();
  }

	function deleteItem(name: string) {
		if (!confirm(`Do you want to delete ${name} from the list?`)) {
			return;
		}
		// Find from array where property name = name
		let index = dogs.findIndex(dog => dog.name === name);
		dogs.splice(index, 1);
		localStorage.setItem('dogs', JSON.stringify(dogs));
		dogs = getList();
	}

	function clearAll() {
		if (!confirm(`Do you want to clear all?\nWARNING: This action cannot be undone. Do you want to continue?`)) {
			return;
		}

		if (!confirm(`Is that your final decision?`)) {
			return;
		}

		if (!confirm(`Okay!`)) {
			alert('Woops you changed your mind! LOL');
			return;
		}
		localStorage.clear();
		dogs = [];
	}

  let categories = ['listed', 'examining', 'finally_back_to_hooman'];
</script>

<section class="min-h-screen py-32">
  <Container>

		<h1 class="h1 text-center mb-20">Pet Manager</h1>

    <!-- Add New Section -->
    <div class="flex justify-end mb-5">
      <div class="flex gap-2">
				<div class="flex items-center">
					<input type="search" class="input rounded-none" bind:value={newPatient} placeholder="Dog Name">
					<button
						on:click={()=>{addItem(newPatient)}}
						type="button"
						class="btn variant-filled-primary rounded-none">Add</button>
				</div>

				<button
					on:click={clearAll}
					type="button"
					class="btn variant-filled-error rounded-none">Clear All</button>
			</div>
    </div>

    <!-- List Section -->
    <div class="grid grid-cols-1 md:grid-cols-3 gap-4">

      {#each categories as category}
        <div class="card p-2">
          <div class="p-2 text-center">
            <h3 class="h3 border-b py-2 capitalize">{category.replace(/_/g, " ")}</h3>
          </div>
          <ul>
            {#each dogs as dog}
              {#if dog.status === category}
                <li class="p-2 items-center flex hover:variant-filled-primary 
                  {category==='finally_back_to_hooman' ? 'font-bold text-success-500' : ''}">
                  <p class="mr-auto">{dog?.name}</p>
									<div class="gap-2">
										{#if category === 'listed'}
										<button class="btn variant-filled" on:click={()=>{deleteItem(dog.name)}}>Remove</button>
										{/if}
										{#if category !== 'finally_back_to_hooman'}
										<button class="btn variant-filled" on:click={()=>{updateStatus(dog.name)}}>Next</button>
										{/if}
									</div>
                </li>
              {/if}
            {/each}
          </ul>
        </div>
      {/each}

    </div>

  </Container>
</section>