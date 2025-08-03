<script lang="ts">
	import {
		Copy,
		Mail,
		Phone,
		MapPin,
		User,
		UserCheck,
		UserPlus,
		UserX,
		UserCog,
		UserMinus,
	} from '@lucide/svelte';

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

	export let contact: Contact;

	function copyToClipboard(text: string) {
		navigator.clipboard.writeText(text);
		// TODO: Add toast notification
	}

	// Profile picture mapping
	const profilePicMap = {
		default: User,
		user1: UserCheck,
		user2: UserPlus,
		user3: UserX,
		user4: UserCog,
		user5: UserMinus,
	};

	function getProfilePicComponent(picId?: string) {
		if (!picId || picId === 'default') return User;
		if (picId.startsWith('user')) {
			return profilePicMap[picId as keyof typeof profilePicMap] || User;
		}
		return null; // For emoji
	}

	function getProfilePicEmoji(picId?: string) {
		if (!picId || picId.startsWith('user') || picId === 'default') return null;

		const emojiMap: Record<string, string> = {
			emoji1: '👤',
			emoji2: '👨',
			emoji3: '👩',
			emoji4: '👨‍💼',
			emoji5: '👩‍💼',
			emoji6: '👨‍💻',
			emoji7: '👩‍💻',
			emoji8: '👨‍🎨',
			emoji9: '👩‍🎨',
			emoji10: '👨‍⚕️',
			emoji11: '👩‍⚕️',
			emoji12: '👨‍🏫',
			emoji13: '👩‍🏫',
			emoji14: '👨‍🔬',
			emoji15: '👩‍🔬',
			emoji16: '👨‍🚀',
			emoji17: '👩‍🚀',
			emoji18: '👨‍🎭',
			emoji19: '👩‍🎭',
			emoji20: '👨‍🎤',
			emoji21: '👩‍🎤',
			emoji22: '👨‍🎪',
			emoji23: '👩‍🎪',
			emoji24: '👨‍🎨',
			emoji25: '👩‍🎨'
		};

		return emojiMap[picId] || null;
	}
</script>

<div class="collapse-arrow collapse bg-base-200 transition-colors border-2 border-black/10">
	<input type="checkbox" />
	<div class="collapse-title flex items-center gap-5 text-lg font-medium">
		<!-- Avatar -->
		<div class="avatar">
			<div class="bg-emoji flex h-9 w-9 items-center justify-center overflow-hidden rounded-full">
				{#if getProfilePicEmoji(contact.profilePic)}
					<span
						class="flex h-full w-full translate-y-0.5 transform items-center justify-center rounded-full text-lg leading-none"
						>{getProfilePicEmoji(contact.profilePic)}</span
					>
				{:else}
					{@const IconComponent = getProfilePicComponent(contact.profilePic)}
					<IconComponent size="20" stroke="1" />
				{/if}
			</div>
		</div>
		<div class="flex-1">
			<div class="font-semibold text-base-content">
				{contact.firstName}
				{contact.lastName}
			</div>
			{#if contact.company}
				<div class="text-sm text-base-content/70">{contact.company}</div>
			{:else}
				<div class="text-sm text-base-content/70">{contact.email}</div>
			{/if}
		</div>
	</div>

	<div class="collapse-content">
		<div
			class="grid grid-cols-1 gap-6 rounded-2xl border-2 border-black/10 bg-base-300 p-4 pt-4 md:grid-cols-2"
		>
			<!-- Contact Information -->
			<div class="space-y-4">
				<h4 class="mb-3 font-semibold text-base-content">Contact Information</h4>

				<div class="space-y-3">
					<div class="flex items-center gap-3">
						<div class="flex h-8 w-8 items-center justify-center rounded-lg bg-primary/10">
							<span class="text-base">
								<Mail size="16" stroke="1" />
							</span>
						</div>
						<div class="flex-1">
							<div class="text-sm text-base-content/70">Email</div>

							<div class="font-medium">{contact.email}</div>
						</div>
						<button
							class="btn btn-ghost btn-sm"
							onclick={() => copyToClipboard(contact.email)}
							title="Copy email"
							aria-label="Copy email"
						>
							<span class="text-base">
								<Copy size="16" stroke="1" />
							</span>
						</button>
					</div>

					<div class="flex items-center gap-3">
						<div class="flex h-8 w-8 items-center justify-center rounded-lg bg-primary/10">
							<span class="text-base">
								<Phone size="16" stroke="1" />
							</span>
						</div>
						<div class="flex-1">
							<div class="text-sm text-base-content/70">Phone</div>
							<div class="font-medium">{contact.phone}</div>
						</div>
						<button
							class="btn btn-ghost btn-sm"
							onclick={() => copyToClipboard(contact.phone)}
							title="Copy phone"
							aria-label="Copy phone"
						>
							<span class="text-base">
								<Copy size="16" stroke="1" />
							</span>
						</button>
					</div>

					<div class="flex items-start gap-3">
						<div class="mt-1 flex h-8 w-8 items-center justify-center rounded-lg bg-primary/10">
							<span class="text-base">
								<MapPin size="16" stroke="1" />
							</span>
						</div>
						<div class="flex-1">
							<div class="text-sm text-base-content/70">Address</div>
							<div class="font-medium">{contact.address}</div>
						</div>
					</div>
				</div>
			</div>

			<!-- Notes and Actions -->
			<div class="space-y-4">
				{#if contact.notes}
					<div>
						<h4 class="mb-3 font-semibold text-base-content">Notes</h4>
						<div class="rounded-lg bg-base-300/50 p-4">
							<p class="text-base-content/80">{contact.notes}</p>
						</div>
					</div>
				{/if}

				<!-- Action Buttons -->
				<div class="flex flex-wrap justify-end gap-2 pt-4">
					<button class="btn btn-sm btn-primary">
						<svg class="h-4 w-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
							<path
								stroke-linecap="round"
								stroke-linejoin="round"
								stroke-width="2"
								d="M11 5H6a2 2 0 00-2 2v11a2 2 0 002 2h11a2 2 0 002-2v-5m-1.414-9.414a2 2 0 112.828 2.828L11.828 15H9v-2.828l8.586-8.586z"
							/>
						</svg>
						Edit
					</button>
					<button class="btn btn-sm btn-primary">
						<svg class="h-4 w-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
							<path
								stroke-linecap="round"
								stroke-linejoin="round"
								stroke-width="2"
								d="M8 12h.01M12 12h.01M16 12h.01M21 12c0 4.418-4.03 8-9 8a9.863 9.863 0 01-4.255-.949L3 20l1.395-3.72C3.512 15.042 3 13.574 3 12c0-4.418 4.03-8 9-8s9 3.582 9 8z"
							/>
						</svg>
						Send Email
					</button>
					<button class="btn bg-red-700 font-extrabold text-white btn-sm">
						<svg class="h-4 w-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
							<path
								stroke-linecap="round"
								stroke-linejoin="round"
								stroke-width="2"
								d="M12 10v6m0 0l-3-3m3 3l3-3m2 8H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z"
							/>
						</svg>
						Delete
					</button>
				</div>
			</div>
		</div>
	</div>
</div>
