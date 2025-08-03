<script lang="ts">
    import { ContactRound, Sun, Moon } from "@lucide/svelte"

	// Props for the header component
	let {contactCount, searchTerm = $bindable(), sortBy = $bindable()} = $props<{
		contactCount: number;
		searchTerm: string;
		sortBy: string;
	}>();

	// Type guard function to check if element is HTMLInputElement
    function isHTMLInputElement(element: any): element is HTMLInputElement {
        return element instanceof HTMLInputElement
    }
  	
  	// Theme toggle handler
  	function handleThemeChange(e: Event) {
  		if (isHTMLInputElement(e.target)) {
  			let target = e.target as HTMLInputElement

  			if (target) {
  				console.log(`Dark Mode: ${target.checked}... // TODO: Store this result to the users table`)
  			} else {
  				console.log("SNAP! Theme change target not found, alert a dev!")
  			}
  		} else {
  			console.log("SNAP! Theme change target is not a HTMLInputElement, alert a dev!")
  		}
  	}

    function addContact() {
        // TODO: Implement add contact functionality
        console.log('Add contact clicked');
    }
</script>

<!-- Header -->
<header class="bg-base-200 backdrop-blur-sm border-b border-base-300 sticky top-0 z-10">
	<div class="container mx-auto px-4 py-6">
		<div class="flex flex-col lg:flex-row lg:items-center lg:justify-between gap-4">
			<!-- Title and Stats -->
			<div class="flex items-center gap-4">
				<div class="flex items-center gap-3">
					<div class="w-12 h-12 bg-primary/10 rounded-xl flex items-center justify-center">
						<ContactRound />
					</div>
					<div>
						<h1 class="text-2xl font-bold text-base-content">Address Book</h1>
						<p class="text-sm text-base-content/70">{contactCount} contacts</p>
					</div>
				</div>
			</div>

			<!-- Search and Actions -->
			<div class="flex flex-col sm:flex-row gap-3">
				<label class="flex cursor-pointer gap-2">
					<Sun />
					<input type="checkbox" value="dark" class="toggle theme-controller" onchange={handleThemeChange} />
					<Moon />
				</label>
			</div>
		</div>
	</div>
</header>
