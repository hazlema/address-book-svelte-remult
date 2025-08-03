<script lang="ts">
	import { X, User, UserCheck, UserPlus, UserX, UserCog, UserMinus, Mail, Phone, MapPin, Building, FileText, Save, Plus } from "@lucide/svelte";
	import ProfilePicPicker from './ProfilePicPicker.svelte';

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
	let profilePic = $state('default');
	let showProfilePicker = $state(false);

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
				notes: notes.trim() || undefined,
				profilePic: profilePic || undefined
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
		profilePic = 'default';
		showProfilePicker = false;
		errors = {};
		
		onClose();
	}

	function handleKeydown(event: KeyboardEvent) {
		if (event.key === 'Escape') {
			handleClose();
		}
	}

	// Profile picture helper functions
	const profilePicMap = {
		'default': User,
		'user1': UserCheck,
		'user2': UserPlus,
		'user3': UserX,
		'user4': UserCog,
		'user5': UserMinus
	};

	function getProfilePicComponent(picId?: string) {
		if (!picId || picId === 'default') return User;
		if (picId.startsWith('user')) {
			return profilePicMap[picId as keyof typeof profilePicMap] || User;
		}
		return User;
	}

	function getProfilePicEmoji(picId?: string) {
		if (!picId || picId.startsWith('user') || picId === 'default') return null;
		
		const emojiMap: Record<string, string> = {
			'emoji1': '👤', 'emoji2': '👨', 'emoji3': '👩', 'emoji4': '👨‍💼', 'emoji5': '👩‍💼',
			'emoji6': '👨‍💻', 'emoji7': '👩‍💻', 'emoji8': '👨‍🎨', 'emoji9': '👩‍🎨', 'emoji10': '👨‍⚕️',
			'emoji11': '👩‍⚕️', 'emoji12': '👨‍🏫', 'emoji13': '👩‍🏫', 'emoji14': '👨‍🔬', 'emoji15': '👩‍🔬',
			'emoji16': '👨‍🚀', 'emoji17': '👩‍🚀', 'emoji18': '👨‍🎭', 'emoji19': '👩‍🎭', 'emoji20': '👨‍🎤',
			'emoji21': '👩‍🎤', 'emoji22': '👨‍🎪', 'emoji23': '👩‍🎪', 'emoji24': '👨‍🎨', 'emoji25': '👩‍🎨'
		};
		
		return emojiMap[picId] || null;
	}

	function getProfilePicLabel(picId?: string) {
		if (!picId || picId === 'default') return 'Default';
		
		const labelMap: Record<string, string> = {
			'user1': 'User 1', 'user2': 'User 2', 'user3': 'User 3', 'user4': 'User 4', 'user5': 'User 5',
			'user6': 'User 6', 'user7': 'User 7',
			'emoji1': 'Person', 'emoji2': 'Man', 'emoji3': 'Woman', 'emoji4': 'Business Man', 'emoji5': 'Business Woman',
			'emoji6': 'Developer', 'emoji7': 'Developer', 'emoji8': 'Artist', 'emoji9': 'Artist', 'emoji10': 'Doctor',
			'emoji11': 'Doctor', 'emoji12': 'Teacher', 'emoji13': 'Teacher', 'emoji14': 'Scientist', 'emoji15': 'Scientist',
			'emoji16': 'Astronaut', 'emoji17': 'Astronaut', 'emoji18': 'Performer', 'emoji19': 'Performer', 'emoji20': 'Singer',
			'emoji21': 'Singer', 'emoji22': 'Circus', 'emoji23': 'Circus', 'emoji24': 'Designer', 'emoji25': 'Designer'
		};
		
		return labelMap[picId] || 'Custom';
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
			<form onsubmit={(e) => { e.preventDefault(); handleSubmit(); }} class="space-y-5">
				<!-- Personal Information -->
				<div class="space-y-3">
					<h3 class="text-base font-semibold text-base-content flex items-center gap-2">
						<User class="w-4 h-4" />
						Personal Information
					</h3>
					
					<div class="grid grid-cols-1 md:grid-cols-2 gap-3">
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

					<!-- Profile Picture -->
					<div class="form-control">
						<label class="label" for="profilePic">
							<span class="label-text font-medium">Profile Picture</span>
						</label>
						<button
							type="button"
							class="btn btn-outline w-full justify-start"
							onclick={() => showProfilePicker = true}
						>
							{#if profilePic === 'default'}
								<User class="w-4 h-4" />
							{:else if profilePic.startsWith('user')}
								{@const IconComponent = getProfilePicComponent(profilePic)}
								<IconComponent class="w-4 h-4" />
							{:else}
								<span class="text-base">{getProfilePicEmoji(profilePic)}</span>
							{/if}
							<span class="ml-2">
								{getProfilePicLabel(profilePic)}
							</span>
						</button>
					</div>
				</div>

				<!-- Contact Information -->
				<div class="space-y-3">
					<h3 class="text-base font-semibold text-base-content flex items-center gap-2">
						<Mail class="w-4 h-4" />
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
				<div class="space-y-3">
					<h3 class="text-base font-semibold text-base-content flex items-center gap-2">
						<FileText class="w-4 h-4" />
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

{#if showProfilePicker}
	<ProfilePicPicker 
		selectedPic={profilePic} 
		onSelect={(pic) => profilePic = pic} 
		onClose={() => showProfilePicker = false} 
	/>
{/if} 