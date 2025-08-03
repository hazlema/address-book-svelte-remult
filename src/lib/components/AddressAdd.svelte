<script lang="ts">
	import { X, User, Mail, Phone, MapPin, Building, FileText, Save, Plus } from "@lucide/svelte";

	interface Contact {
		id: number;
		firstName: string;
		lastName: string;
		email: string;
		phone: string;
		address: string;
		company?: string;
		notes?: string;
	}

	// Props
	let { onClose, onSave } = $props<{
		onClose: () => void;
		onSave: (contact: Omit<Contact, 'id'>) => void;
	}>();

	// Form state
	let firstName = $state('');
	let lastName = $state('');
	let email = $state('');
	let phone = $state('');
	let address = $state('');
	let company = $state('');
	let notes = $state('');

	// Form validation
	let errors = $state<Record<string, string>>({});

	function validateForm() {
		errors = {};
		
		if (!firstName.trim()) errors.firstName = 'First name is required';
		if (!lastName.trim()) errors.lastName = 'Last name is required';
		if (!email.trim()) {
			errors.email = 'Email is required';
		} else if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email)) {
			errors.email = 'Please enter a valid email address';
		}
		if (!phone.trim()) errors.phone = 'Phone number is required';
		if (!address.trim()) errors.address = 'Address is required';

		return Object.keys(errors).length === 0;
	}

	function handleSubmit() {
		if (validateForm()) {
			const newContact: Omit<Contact, 'id'> = {
				firstName: firstName.trim(),
				lastName: lastName.trim(),
				email: email.trim(),
				phone: phone.trim(),
				address: address.trim(),
				company: company.trim() || undefined,
				notes: notes.trim() || undefined
			};
			
			onSave(newContact);
			handleClose();
		}
	}

	function handleClose() {
		// Reset form
		firstName = '';
		lastName = '';
		email = '';
		phone = '';
		address = '';
		company = '';
		notes = '';
		errors = {};
		
		onClose();
	}

	function handleKeydown(event: KeyboardEvent) {
		if (event.key === 'Escape') {
			handleClose();
		}
	}
</script>

<svelte:window onkeydown={handleKeydown} />

<!-- Modal Overlay -->
<div class="fixed inset-0 bg-black/50 backdrop-blur-sm z-50 flex items-center justify-center p-4">
	<!-- Modal Content -->
	<div class="bg-base-100 rounded-2xl shadow-2xl w-full max-w-2xl max-h-[90vh] overflow-hidden">
		<!-- Header -->
		<div class="flex items-center justify-between p-6 border-b border-base-300">
			<div class="flex items-center gap-3">
				<div class="w-10 h-10 bg-primary/10 rounded-xl flex items-center justify-center">
					<Plus class="w-5 h-5 text-primary" />
				</div>
				<div>
					<h2 class="text-xl font-bold text-base-content">Add New Contact</h2>
					<p class="text-sm text-base-content/70">Fill in the details below</p>
				</div>
			</div>
			<button 
				class="btn btn-ghost btn-sm btn-circle"
				onclick={handleClose}
				aria-label="Close modal"
			>
				<X class="w-5 h-5" />
			</button>
		</div>

		<!-- Form Content -->
		<div class="p-6 overflow-y-auto max-h-[calc(90vh-140px)]">
			<form onsubmit={(e) => { e.preventDefault(); handleSubmit(); }} class="space-y-6">
				<!-- Personal Information -->
				<div class="space-y-4">
					<h3 class="text-lg font-semibold text-base-content flex items-center gap-2">
						<User class="w-5 h-5" />
						Personal Information
					</h3>
					
					<div class="grid grid-cols-1 md:grid-cols-2 gap-4">
						<!-- First Name -->
						<div class="form-control">
							<label class="label" for="firstName">
								<span class="label-text font-medium">First Name *</span>
							</label>
							<input
								type="text"
								class="input input-bordered w-full {errors.firstName ? 'input-error' : ''}"
								placeholder="Enter first name"
								bind:value={firstName}
								required
							/>
							{#if errors.firstName}
								<label class="label" for="firstName">
									<span class="label-text-alt text-error">{errors.firstName}</span>
								</label>
							{/if}
						</div>

						<!-- Last Name -->
						<div class="form-control">
							<label class="label" for="lastName">
								<span class="label-text font-medium">Last Name *</span>
							</label>
							<input
								type="text"
								class="input input-bordered w-full {errors.lastName ? 'input-error' : ''}"
								placeholder="Enter last name"
								bind:value={lastName}
								required
							/>
							{#if errors.lastName}
								<label class="label" for="lastName">
									<span class="label-text-alt text-error">{errors.lastName}</span>
								</label>
							{/if}
						</div>
					</div>

					<!-- Company -->
					<div class="form-control">
						<label class="label">
							<span class="label-text font-medium flex items-center gap-2">
								<Building class="w-4 h-4" />
								Company
							</span>
						</label>
						<input
							type="text"
							class="input input-bordered w-full"
							placeholder="Enter company name (optional)"
							bind:value={company}
						/>
					</div>
				</div>

				<!-- Contact Information -->
				<div class="space-y-4">
					<h3 class="text-lg font-semibold text-base-content flex items-center gap-2">
						<Mail class="w-5 h-5" />
						Contact Information
					</h3>
					
					<!-- Email -->
					<div class="form-control">
						<label class="label" for="email">
							<span class="label-text font-medium">Email Address *</span>
						</label>
						<input
							type="email"
							class="input input-bordered w-full {errors.email ? 'input-error' : ''}"
							placeholder="Enter email address"
							bind:value={email}
							required
						/>
						{#if errors.email}
							<label class="label" for="email">
								<span class="label-text-alt text-error">{errors.email}</span>
							</label>
						{/if}
					</div>

					<!-- Phone -->
					<div class="form-control">
						<label class="label">
							<span class="label-text font-medium flex items-center gap-2">
								<Phone class="w-4 h-4" />
								Phone Number *
							</span>
						</label>
						<input
							type="tel"
							class="input input-bordered w-full {errors.phone ? 'input-error' : ''}"
							placeholder="Enter phone number"
							bind:value={phone}
							required
						/>
						{#if errors.phone}
							<label class="label" for="phone">
								<span class="label-text-alt text-error">{errors.phone}</span>
							</label>
						{/if}
					</div>

					<!-- Address -->
					<div class="form-control">
						<label class="label" for="address">
							<span class="label-text font-medium flex items-center gap-2">
								<MapPin class="w-4 h-4" />
								Address *
							</span>
						</label>
						<textarea
							class="textarea textarea-bordered w-full {errors.address ? 'textarea-error' : ''}"
							placeholder="Enter full address"
							bind:value={address}
							rows="3"
							required
						></textarea>
						{#if errors.address}
							<label class="label" for="address">
								<span class="label-text-alt text-error">{errors.address}</span>
							</label>
						{/if}
					</div>
				</div>

				<!-- Notes -->
				<div class="space-y-4">
					<h3 class="text-lg font-semibold text-base-content flex items-center gap-2">
						<FileText class="w-5 h-5" />
						Additional Information
					</h3>
					
					<div class="form-control">
						<label class="label" for="notes">
							<span class="label-text font-medium">Notes</span>
						</label>
						<textarea
							class="textarea textarea-bordered w-full"
							placeholder="Add any additional notes about this contact (optional)"
							bind:value={notes}
							rows="4"
						></textarea>
					</div>
				</div>
			</form>
		</div>

		<!-- Footer -->
		<div class="flex items-center justify-end gap-3 p-6 border-t border-base-300 bg-base-200/50">
			<button 
				class="btn btn-ghost"
				onclick={handleClose}
			>
				Cancel
			</button>
			<button 
				class="btn btn-primary"
				onclick={handleSubmit}
			>
				<Save class="w-4 h-4" />
				Save Contact
			</button>
		</div>
	</div>
</div> 