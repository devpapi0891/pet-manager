<script lang="ts">
  import {Container} from '$lib/components';
  import { onMount } from 'svelte';

  type pet = {
    name: string,
    status: string
  }

  let pets: pet[] = [];
  $: newPatient = '';

  onMount(() => {
    if (!localStorage.getItem('pets')) {
      // localStorage.setItem('pets', '[]');
      localStorage.setItem('pets', '[{"name":"Cookie","status":"listed"},{"name":"Snoopy","status":"listed"},{"name":"Scooby","status":"listed"},{"name":"Daisy","status":"listed"}]');
    }
    pets = getList();
  });

  function getList() {
    let stored: any = localStorage.getItem('pets');
    return JSON.parse(stored);
  }

  function addItem(name: string = '') {
    let status = 'listed'; //initial state
    

    if (name.trim() == '') {
      alert('Please enter a name');
      return;
    }

    if (pets.some(pet => pet.name === name.trim())) {
      alert('pet name exists');
      return;
    }

    pets = [...pets, { name: name.trim(), status }];
    localStorage.setItem('pets', JSON.stringify(pets));
    pets = getList();
    newPatient = '';

  }

  function updateStatus(name: string) {
		// Find from array where property name = name
		let index = pets.findIndex(pet => pet.name === name);
    let status = pets[index].status;

		if (!confirm(`Update pet patient ${name}'s status?`)) {
			return;
		}
		
    switch (status) {
      case 'listed':
        pets[index].status = pets[index].status = 'examining';
        break;
        
      default:
        pets[index].status = pets[index].status = 'finally_back_to_hooman';
        break;
    }
    
    localStorage.setItem('pets', JSON.stringify(pets));
    pets = getList();
  }

	function deleteItem(name: string) {
		if (!confirm(`Do you want to delete ${name} from the list?`)) {
			return;
		}
		// Find from array where property name = name
		let index = pets.findIndex(pet => pet.name === name);
		pets.splice(index, 1);
		localStorage.setItem('pets', JSON.stringify(pets));
		pets = getList();
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
		pets = [];
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
					<input type="search" class="input rounded-none" bind:value={newPatient} placeholder="Pet Name">
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
            {#each pets as pet}
              {#if pet.status === category}
                <li class="p-2 items-center flex hover:variant-filled-primary 
                  {category==='finally_back_to_hooman' ? 'font-bold text-success-500' : ''}">
                  <p class="mr-auto">{pet?.name}</p>
									<div class="gap-2">
										{#if category === 'listed'}
										<button class="btn variant-filled-error" on:click={()=>{deleteItem(pet.name)}}>Remove</button>
										{/if}
										{#if category !== 'finally_back_to_hooman'}
										<button class="btn variant-filled" on:click={()=>{updateStatus(pet.name)}}>Next</button>
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