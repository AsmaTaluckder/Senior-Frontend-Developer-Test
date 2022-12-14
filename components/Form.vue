<template>
	<div>
		<div
			:class="[
				'[ form-tab-wrapper card-base ] overflow-hidden py-0 opacity-0 pointer-events-none cursor-default transform translate-y-0 transition-all duration-300 bg-white text-grey-300',
				formActive && 'is-active',
				!editMode && formActive && '-mt-20'
			]"
		>
			<div class="flex items-center justify-between">
				<p v-if="editMode" class="font-bold">Edit Location</p>
				<p v-else class="font-bold">New Location</p>
				<button class="p-2 -m-2" @click="$emit('close-form')">
					<times class="text-grey-200" />
				</button>
			</div>
			<div class="text-white">

				<div
					:class="[
						'[ color-menu ] mt-2 overflow-hidden transition-all duration-300',
						colorMenu && '[ color-menu--active ]'
					]"
				>
					<button
						v-for="color in colors"
						:id="color.id"
						:key="color.id"
						:tabindex="!colorMenu && -1"
						:class="[
							`[ colorbox ] bg-${color.colorClass} relative rounded-lg py-16 mt-2 w-full cursor-pointer transition-all duration-300`,
							form.color === color.colorClass && 'pointer-events-none'
						]"
						@click="onColorChange(color.colorClass)"
					>
						<div
							:class="[
								'transition-opacity duration-300',
								form.color === color.colorClass
									? 'opacity-1 cursor-default'
									: 'opacity-0'
							]"
						>
							<div class="absolute inset-0 bg-white opacity-25" />
							<check class="[ selected-icon ] text-white absolute" />
						</div>
					</button> 
				</div>
			</div>
			<ValidationObserver v-slot="{ invalid, reset }">
				<form
					class="[ form-wrapper ]"
					@submit.prevent="
						() => {
							handleSubmit();
							reset();
						}
					"
				>
					<input-base
						:initial-value="form.title"
						rules="required"
						label="Title *"
						@input="
							v => {
								form.title = v;
							}
						"
					/>
					<input-base
						:initial-value="form.address"
						rules="required"
						label="Enter the address *"
						@input="
							v => {
								form.address = v;
							}
						"
					/>
					<p class="text-xs text-blue uppercase mt-8">Contact information</p>
					<hr class="border-grey-100 mt-3" />
					<input-base
						:initial-value="form.name"
						rules="required|alpha_spaces"
						label="Full name *"
						@input="
							v => {
								form.name = v;
							}
						"
					/>
					<input-base
						:initial-value="form.position"
						rules="required"
						label="Job position *"
						@input="
							v => {
								form.position = v;
							}
						"
					/>
					<input-base
						:initial-value="form.email"
						rules="required|email"
						label="Email address *"
						placeholder="name@example.com"
						@input="
							v => {
								form.email = v;
							}
						"
					/>
					<input-base
						:initial-value="form.phone"
						rules="required|min:14"
						placeholder="(xxx) xxx-xxxx"
						label="Phone *"
						mask="(###) ###-####"
						@input="
							v => {
								form.phone = v;
							}
						"
					/>
					<button
						:class="[
							'rounded py-2 px-6 mt-6 bg-blue text-white transition-all duration-300',
							invalid && 'bg-grey-200 cursor-default'
						]"
						type="submit"
						:disabled="invalid"
					>
						Save
					</button>
				</form>
			</ValidationObserver>
		</div>
	</div>
</template>

<script>
import { ValidationObserver } from "vee-validate";
import InputBase from "./InputBase";

import Times from "~/icons/Times";
import AngleDown from "~/icons/AngleDown";
import Check from "~/icons/Check";

export default {
	components: {
		InputBase,
		ValidationObserver,
		Times,
		AngleDown,
		Check
	},
	props: {
		formActive: {
			type: Boolean,
			required: true,
			default: false
		},
		office: {
			type: Object,
			required: false
		},
		editMode: {
			type: Boolean,
			required: false,
			default: false
		}
	},
	data() {
		return {
			colorMenu: false,
			colors: [
				{ colorClass: "yellow", id: 1 },
				{ colorClass: "red", id: 2 },
				{ colorClass: "blue", id: 3 },
				{ colorClass: "grey-200", id: 4 },
				{ colorClass: "grey-300", id: 5 }
			],
			form: {
				color: "yellow",
				title: "",
				address: "",
				name: "",
				position: "",
				email: "",
				phone: ""
			}
		};
	},
	methods: {
		onColorChange(color) {
			this.form.color = color;
			this.colorMenu = false;
		},
		handleSubmit() {
			if (this.editMode) {
				this.$emit("save-office", { ...this.form });
			} else {
				this.$emit("add-office", { ...this.form, deleted: false });
				this.resetForm();
			}
		},
		resetForm() {
			this.form = {
				color: "yellow",
				title: "",
				address: "",
				name: "",
				position: "",
				email: "",
				phone: "",
				deleted: false
			};
		}
	},
	mounted() {
		this.form = { ...this.office };
	}
};
</script>

<style>
.form-tab-wrapper {
	max-height: 0;
}

.form-tab-wrapper.is-active {
	max-height: 2000px;
	@apply py-6;
	@apply opacity-100;
	@apply pointer-events-auto;
	@apply -translate-y-6;
	@apply cursor-auto;
}

.form-group {
	@apply mt-6;
}

.form-group input {
	border-radius: 0.25rem;
}

.form-group input:focus {
	@apply outline-none;
	@apply border-blue;
}

.form-group.is-invalid input {
	@apply border-red;
	@apply text-red;
}

.form-group.is-invalid input:focus {
	@apply border-blue;
	@apply text-grey-300;
}

.color-menu {
	max-height: 0;
}

.color-menu--active {
	max-height: 700px;
}

.color-menu .selected-icon {
	top: 50%;
	left: 50%;
	transform: translateX(-50%) translateY(-50%);
}

.error-icon {
	right: 3%;
	top: 30%;
}

.form-group.is-invalid .error-icon {
	@apply opacity-100;
}
</style>
