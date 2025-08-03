<script lang="ts">
	import Header from '$lib/components/Header.svelte';
	import AddressView from '$lib/components/AddressView.svelte';
	import AddressAdd from '$lib/components/AddressAdd.svelte';

	interface Contact {
		id: number;
		firstName: string;
		lastName: string;
		email: string;
		phone: string;
		address: string;
		company?: string;
		notes?: string;
		profilePic?: string;
	}

	// Sample contact data
	let contacts: Contact[] = $state([
		{
			id: 1,
			firstName: 'John',
			lastName: 'Doe',
			email: 'john.doe@email.com',
			phone: '+1 (555) 123-4567',
			address: '123 Main St, Anytown, USA',
			company: 'Tech Solutions Inc.',
			notes: 'Met at the conference last month. Great networking opportunity.',
			profilePic: 'emoji4'
		},
		{
			id: 2,
			firstName: 'Sarah',
			lastName: 'Johnson',
			email: 'sarah.johnson@email.com',
			phone: '+1 (555) 987-6543',
			address: '456 Oak Ave, Somewhere, USA',
			company: 'Design Studio',
			notes: 'Freelance designer. Excellent work on the logo project.',
			profilePic: 'emoji9'
		},
		{
			id: 3,
			firstName: 'Michael',
			lastName: 'Chen',
			email: 'michael.chen@email.com',
			phone: '+1 (555) 456-7890',
			address: '789 Pine Rd, Elsewhere, USA',
			company: 'StartupXYZ',
			notes: 'Co-founder. Working on innovative AI solutions.',
			profilePic: 'emoji6'
		},
		{
			id: 4,
			firstName: 'Emily',
			lastName: 'Williams',
			email: 'emily.williams@email.com',
			phone: '+1 (555) 321-0987',
			address: '321 Elm St, Nowhere, USA',
			company: 'Marketing Pro',
			notes: 'Marketing consultant. Helped with the campaign strategy.',
			profilePic: 'emoji5'
		},
		{
			id: 5,
			firstName: 'David',
			lastName: 'Brown',
			email: 'david.brown@email.com',
			phone: '+1 (555) 654-3210',
			address: '654 Maple Dr, Anywhere, USA',
			company: 'Consulting Group',
			notes: 'Business consultant. Valuable insights on growth strategy.',
			profilePic: 'user1'
		}
	]);

	let searchTerm     = $state('');
	let sortBy         = $state('first');

	let modalData = $state<{
		status: 'closed' | 'open';
		action: 'add' | 'update';
		contact?: Contact;
		accept: (contact: Omit<Contact, 'id'>) => void;
		reject: () => void;
	}>({
		status: 'closed',
		action: 'add',
		contact: undefined,
		accept: () => {},
		reject: () => {}
	})

	function handleAddOrEditAddress(contact: Contact) {
		modalData = {
			status: 'open',
			action: 'update',
			contact: contact,
			accept: handleUpdateAddress,
			reject: () => { modalData.status = 'closed'; }
		};
	}

	function handleAddAddress(contact: Omit<Contact, 'id'>) {
		console.log(contact);

		const newContact: Contact = {
			...contact,
			id: Math.max(...contacts.map(c => c.id), 0) + 1
		};

		contacts.push(newContact);
		modalData.status = 'closed';
	}

	function handleUpdateAddress(contact: Omit<Contact, 'id'>) {
		console.log('Updating contact:', contact);
		
		if (modalData.contact) {
			const updatedContact: Contact = {
				...contact,
				id: modalData.contact.id
			};
			
			const index = contacts.findIndex(c => c.id === modalData.contact!.id);
			if (index !== -1) {
				contacts[index] = updatedContact;
			}
		}
		
		modalData.status = 'closed';
	}

	function setupEditModal(contact: Contact) {
		modalData = {
			status: 'open',
			action: 'update',
			contact: contact,
			accept: handleUpdateAddress,
			reject: () => { modalData.status = 'closed'; }
		};
	}
	
	// Computed values -- NEVER COMPUTE VALUES IN AN EFFECT!
	let filteredContacts = $derived(
		contacts.filter(
			(contact: Contact) =>
				contact.lastName.toLowerCase().includes(searchTerm.toLowerCase()) ||
				contact.firstName.toLowerCase().includes(searchTerm.toLowerCase()) ||
				contact.email.toLowerCase().includes(searchTerm.toLowerCase())
		)
	);

	let sortedContacts = $derived(
		[...filteredContacts].sort((a: Contact, b: Contact) => {
			if (sortBy === 'lastName') {
				return a.lastName.localeCompare(b.lastName);
			} else if (sortBy === 'firstName') {
				return a.firstName.localeCompare(b.firstName);
			}
			return 0;
		})
	);

	let contactCount = $derived(sortedContacts.length);
</script>

<svelte:head>
	<title>Address Book</title>
</svelte:head>

<div class="bg-surface mx-auto min-h-screen w-4xl border-r-2 border-l-2 border-base-300">
	<Header {contactCount} bind:searchTerm bind:sortBy />

	<!-- Search and Filters Section -->
	<div class="justify-center border-b border-base-300 bg-base-200/50">
		<div class="container mx-auto px-4 py-4">
			<div class="flex flex-col items-center justify-center gap-3 sm:flex-row">
				<!-- Search -->
				<div class="join">
					<div class="join-item">
						<input
							type="text"
							placeholder="Search contacts by name, email, or phone..."
							class="input-bordered input join-item w-full sm:w-64"
							bind:value={searchTerm}
						/>
					</div>
					<button aria-label="Search" class="btn join-item btn-primary">
						<svg class="h-4 w-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
							<path
								stroke-linecap="round"
								stroke-linejoin="round"
								stroke-width="2"
								d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"
							/>
						</svg>
					</button>
				</div>

				<!-- Sort Dropdown -->
				<select class="select-bordered select" bind:value={sortBy}>
					<option value="lastName">Sort by Last Name</option>
					<option value="firstName">Sort by First Name</option>
				</select>

				<!-- Add Contact Button -->
				<button
					class="btn btn-primary"
					onclick={() => {
						modalData = {
							status: 'open',
							action: 'add',
							contact: undefined,
							accept: handleAddAddress,
							reject: () => { modalData.status = 'closed'; }
						};
					}}
				>
					<svg class="h-4 w-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
						<path
							stroke-linecap="round"
							stroke-linejoin="round"
							stroke-width="2"
							d="M12 6v6m0 0v6m0-6h6m-6 0H6"
						/>
					</svg>
					Add Contact
				</button>
			</div>
		</div>
	</div>

	<!-- Main Content -->
	<main class="bg-surface container mx-auto px-4 py-8">
		{#if sortedContacts.length === 0}
			<!-- Empty State -->
			<div class="py-16 text-center">
				<div
					class="mx-auto mb-6 flex h-24 w-24 items-center justify-center rounded-full bg-base-200"
				>
					<svg
						class="h-12 w-12 text-base-content/50"
						fill="none"
						stroke="currentColor"
						viewBox="0 0 24 24"
					>
						<path
							stroke-linecap="round"
							stroke-linejoin="round"
							stroke-width="2"
							d="M17 20h5v-2a3 3 0 00-5.356-1.857M17 20H7m10 0v-2c0-.656-.126-1.283-.356-1.857M7 20H2v-2a3 3 0 015.356-1.857M7 20v-2c0-.656.126-1.283.356-1.857m0 0a5.002 5.002 0 019.288 0M15 7a3 3 0 11-6 0 3 3 0 016 0zm6 3a2 2 0 11-4 0 2 2 0 014 0zM7 10a2 2 0 11-4 0 2 2 0 014 0z"
						/>
					</svg>
				</div>
				<h3 class="mb-2 text-xl font-semibold text-base-content">
					{searchTerm ? 'No contacts found' : 'No contacts yet'}
				</h3>
				<p class="mb-6 text-base-content/70">
					{searchTerm
						? 'Try adjusting your search terms'
						: 'Get started by adding your first contact'}
				</p>
				{#if !searchTerm}
					<button class="btn btn-primary" onclick={() => {
						modalData = {
							status: 'open',
							action: 'add',
							contact: undefined,
							accept: handleAddAddress,
							reject: () => { modalData.status = 'closed'; }
						};
					}}>
						<svg class="h-4 w-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
							<path
								stroke-linecap="round"
								stroke-linejoin="round"
								stroke-width="2"
								d="M12 6v6m0 0v6m0-6h6m-6 0H6"
							/>
						</svg>
						Add Your First Contact
					</button>
				{/if}
			</div>
		{:else}
			<!-- Contact List -->
			<div class="space-y-3">
				{#each sortedContacts as contact (contact.id)}
					<AddressView {contact} bind:modalData {setupEditModal} />
				{/each}
			</div>
		{/if}
	</main>
</div>
{#if modalData.status === 'open'}
	<AddressAdd bind:modalData />
{/if}
