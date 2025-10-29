<script lang="ts">
	type Person = {
		name: string
		surname: string
	}

	let people = $state([
		{ name: 'Rich', surname: 'Harris' },
		{ name: 'Rayan', surname: 'Carniato' },
		{ name: 'Evan', surname: 'You' }
	])

	let selected = $state<Person>()
	let person = $state({ name: '', surname: '' })

	let prefix = $state('')
	const filteredPeople = $derived(
		prefix
			? people.filter((p) =>
					p.surname.toLocaleLowerCase().startsWith(prefix.toLocaleLowerCase())
			  )
			: people
	)

	$effect(() => {
		person = {
			name: selected?.name ?? '',
			surname: selected?.surname ?? ''
		}
	})

	function createPerson() {
		people.push(person)
		clearFields()
	}

	function updateperson() {
		const index = people.indexOf(selected!)
		people[index] = { name: person.name, surname: person.surname }
	}

	function deletePerson() {
		people = people.filter((p) => p.name !== person.name || p.surname !== person.surname)
		clearFields()
	}

	function clearFields() {
		person = { name: '', surname: '' }
	}

	function selectPerson(p: Person) {
		selected = p
	}
</script>

<div
	class="min-h-screen w-full flex items-center justify-center bg-linear-to-br from-[#0A0A0C] via-[#0F0F11] to-[#121214] p-6">

	<div
		class="w-full max-w-[900px] p-6 rounded-2xl bg-linear-to-br from-[#1C1C1E] via-[#141416] to-[#0A0A0C] shadow-[0_0_35px_rgba(0,0,0,0.45)] border border-[#2a2a2e] text-gray-200 space-y-6 transition duration-300">

		<h2 class="text-2xl font-bold text-center text-white tracking-wide">
			CRUD
		</h2>

		<!-- Filter -->
		<div class="space-y-2">
			<label for="prefix" class="text-sm text-gray-400">Search by surname</label>
			<input
				id="prefix"
				bind:value={prefix}
				class="w-full bg-[#202024] border border-[#2a2a2e] rounded-lg px-3 py-2 focus:outline-none focus:ring-2 focus:ring-indigo-500 transition"
				placeholder="Start typing…" />
		</div>

		<!-- People list + details -->
		<div class="grid grid-cols-2 gap-6">
			<!-- People List (Custom) -->
			<div class="space-y-2">
				<div class="text-sm text-gray-400">People</div>
				<div
					role="listbox"
					tabindex="0"
					class="w-full h-[200px] bg-[#202024] border border-[#2a2a2e] rounded-lg overflow-y-auto focus-within:ring-2 focus-within:ring-indigo-500">
					{#each filteredPeople as p}
						<div
							role="option"
							aria-selected={selected === p}
							tabindex="0"
							onclick={() => selectPerson(p)}
							onkeydown={(e) => e.key === 'Enter' && selectPerson(p)}
							class="px-3 py-2 cursor-pointer transition-colors text-white select-none [&::selection]:bg-transparent [&::selection]:text-white [&::-moz-selection]:bg-transparent [&::-moz-selection]:text-white {selected === p ? 'bg-linear-to-r from-indigo-600 to-indigo-500' : 'hover:bg-[#2e2e32]'}">
							{p.surname}, {p.name}
						</div>
					{/each}
				</div>
			</div>

			<!-- Details -->
			<div class="space-y-4">
				<div class="space-y-1">
					<label class="text-sm text-gray-400" for="person-name">Name</label>
					<input
						id="person-name"
						bind:value={person.name}
						class="w-full bg-[#202024] border border-[#2a2a2e] rounded-lg px-3 py-2 focus:outline-none focus:ring-2 focus:ring-indigo-500 transition"
						placeholder="John" />
				</div>

				<div class="space-y-1">
					<label class="text-sm text-gray-400" for="person-surname">Surname</label>
					<input
						id="person-surname"
						bind:value={person.surname}
						class="w-full bg-[#202024] border border-[#2a2a2e] rounded-lg px-3 py-2 focus:outline-none focus:ring-2 focus:ring-indigo-500 transition"
						placeholder="Smith" />
				</div>
			</div>
		</div>

		<!-- Buttons -->
		<div class="flex justify-between gap-3 pt-2">
			<button
				onclick={createPerson}
				class="cursor-pointer flex-1 bg-indigo-600 hover:bg-indigo-700 px-3 py-2 font-medium rounded-lg text-white shadow-md transition">
			    Create
			</button>
			<button
				onclick={updateperson}
				class="cursor-pointer flex-1 bg-blue-600 hover:bg-blue-700 px-3 py-2 font-medium rounded-lg text-white shadow-md transition">
				Update
			</button>
			<button
				onclick={deletePerson}
				class="cursor-pointer flex-1 bg-purple-700 hover:bg-purple-800 px-3 py-2 font-medium rounded-lg text-white shadow-md transition">
				Delete
			</button>
		</div>
	</div>
</div>