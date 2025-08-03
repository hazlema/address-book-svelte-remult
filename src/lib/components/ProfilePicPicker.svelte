<script lang="ts">
	import { X, User, UserCheck, UserPlus, UserX, UserCog, UserMinus, UserCheck2, UserCog2 } from "@lucide/svelte";

	// Props
	let { selectedPic, onSelect, onClose } = $props<{
		selectedPic?: string;
		onSelect: (pic: string) => void;
		onClose: () => void;
	}>();

	// Profile picture options
	const profilePics = [
		{ id: 'default', icon: User, label: 'Default' },
		{ id: 'user1', icon: UserCheck, label: 'User 1' },
		{ id: 'user2', icon: UserPlus, label: 'User 2' },
		{ id: 'user3', icon: UserX, label: 'User 3' },
		{ id: 'user4', icon: UserCog, label: 'User 4' },
		{ id: 'user5', icon: UserMinus, label: 'User 5' },
		{ id: 'user6', icon: UserCheck2, label: 'User 6' },
		{ id: 'user7', icon: UserCog2, label: 'User 7' },
		// Emoji options
		{ id: 'emoji1', emoji: '👤', label: 'Person' },
		{ id: 'emoji2', emoji: '👨', label: 'Man' },
		{ id: 'emoji3', emoji: '👩', label: 'Woman' },
		{ id: 'emoji4', emoji: '👨‍💼', label: 'Business Man' },
		{ id: 'emoji5', emoji: '👩‍💼', label: 'Business Woman' },
		{ id: 'emoji6', emoji: '👨‍💻', label: 'Developer' },
		{ id: 'emoji7', emoji: '👩‍💻', label: 'Developer' },
		{ id: 'emoji8', emoji: '👨‍🎨', label: 'Artist' },
		{ id: 'emoji9', emoji: '👩‍🎨', label: 'Artist' },
		{ id: 'emoji10', emoji: '👨‍⚕️', label: 'Doctor' },
		{ id: 'emoji11', emoji: '👩‍⚕️', label: 'Doctor' },
		{ id: 'emoji12', emoji: '👨‍🏫', label: 'Teacher' },
		{ id: 'emoji13', emoji: '👩‍🏫', label: 'Teacher' },
		{ id: 'emoji14', emoji: '👨‍🔬', label: 'Scientist' },
		{ id: 'emoji15', emoji: '👩‍🔬', label: 'Scientist' },
		{ id: 'emoji16', emoji: '👨‍🚀', label: 'Astronaut' },
		{ id: 'emoji17', emoji: '👩‍🚀', label: 'Astronaut' },
		{ id: 'emoji18', emoji: '👨‍🎭', label: 'Performer' },
		{ id: 'emoji19', emoji: '👩‍🎭', label: 'Performer' },
		{ id: 'emoji20', emoji: '👨‍🎤', label: 'Singer' },
		{ id: 'emoji21', emoji: '👩‍🎤', label: 'Singer' },
		{ id: 'emoji22', emoji: '👨‍🎪', label: 'Circus' },
		{ id: 'emoji23', emoji: '👩‍🎪', label: 'Circus' },
		{ id: 'emoji24', emoji: '👨‍🎨', label: 'Designer' },
		{ id: 'emoji25', emoji: '👩‍🎨', label: 'Designer' }
	];

	function handleSelect(picId: string) {
		onSelect(picId);
		onClose();
	}

	function handleKeydown(event: KeyboardEvent) {
		if (event.key === 'Escape') {
			onClose();
		}
	}
</script>

<svelte:window onkeydown={handleKeydown} />

<!-- Modal Overlay -->
<div class="fixed inset-0 bg-black/50 backdrop-blur-sm z-50 flex items-center justify-center p-4">
	<!-- Modal Content -->
	<div class="bg-base-100 rounded-2xl shadow-2xl w-full max-w-md">
		<!-- Header -->
		<div class="flex items-center justify-between p-6 border-b border-base-300">
			<div class="flex items-center gap-3">
				<div class="w-10 h-10 bg-primary/10 rounded-xl flex items-center justify-center">
					<User class="w-5 h-5 text-primary" />
				</div>
				<div>
					<h2 class="text-xl font-bold text-base-content">Choose Profile Picture</h2>
					<p class="text-sm text-base-content/70">Select a profile picture for your contact</p>
				</div>
			</div>
			<button 
				class="btn btn-ghost btn-sm btn-circle"
				onclick={onClose}
				aria-label="Close modal"
			>
				<X class="w-5 h-5" />
			</button>
		</div>

		<!-- Content -->
		<div class="p-6">
			<div class="grid grid-cols-6 gap-3">
				{#each profilePics as pic}
					<button
						class="w-12 h-12 rounded-full border-2 transition-all duration-200 hover:scale-110 hover:shadow-md flex items-center justify-center {(selectedPic || 'default') === pic.id ? 'border-primary bg-primary/10' : 'border-base-300 hover:border-primary/50'}"
						onclick={() => handleSelect(pic.id)}
						title={pic.label}
						aria-label="Select {pic.label} profile picture"
					>
						{#if pic.icon}
							{@const IconComponent = pic.icon}
							<IconComponent class="w-6 h-6 text-base-content" />
						{:else if pic.emoji}
							<span class="text-xl">{pic.emoji}</span>
						{/if}
					</button>
				{/each}
			</div>
			
			{#if selectedPic}
				<div class="mt-4 text-sm text-base-content/70 text-center">
					Selected: {profilePics.find(p => p.id === (selectedPic || 'default'))?.label || 'Custom'}
				</div>
			{/if}
		</div>

		<!-- Footer -->
		<div class="flex items-center justify-end gap-3 p-6 border-t border-base-300 bg-base-200/50">
			<button 
				class="btn btn-ghost"
				onclick={onClose}
			>
				Cancel
			</button>
		</div>
	</div>
</div> 