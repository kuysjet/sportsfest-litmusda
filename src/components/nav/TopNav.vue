<template>
	<v-app-bar color="black" :class="{ 'pl-5': $vuetify.display.mdAndUp }">
		<v-app-bar-nav-icon
			v-if="$vuetify.display.smAndDown"
			@click.stop="$store.state.app.sideNav = !$store.state.app.sideNav"
		/>
		<h3 v-if="$vuetify.display.mdAndUp" id="topnav">{{ $store.getters.appName }}</h3>
		<h4 v-else-if="$vuetify.display.smAndDown" id="topnav">{{ $store.getters.appName }}</h4>
		<v-spacer/>
		<div v-if="$store.getters['auth/getUser'] !== null">
			<v-chip
				:color="$store.getters['auth/getUser'] !== null ?
					$store.getters['auth/getUser'].userType === 'admin' ? 'amber' :
					$store.getters['auth/getUser'].userType === 'judge' ? 'green-lighten-2' :
					'red-lighten-2' : ''"
				:style="$vuetify.display.mdAndDown ? 'font-size: 12px' : ''"
			>
				<v-icon start icon="mdi-account-circle"/>
				{{ $store.getters['auth/getUser'].name }}
			</v-chip>

			<v-avatar
				size="35"
				v-if="$vuetify.display.mdAndUp"
				:class="$vuetify.display.mdAndUp ? 'ms-3' : ''"
			>
				<v-img
					:src="`${$store.getters.appURL}/crud/uploads/${$store.getters['auth/getUser'].avatar}`"
				/>
			</v-avatar>
		</div>

		<!--	Sign out	-->
		<v-dialog
			v-model="dialog"
			max-width="400"
		>
			<template v-slot:activator="{ props }">
				<v-menu>
					<template
						v-slot:activator="{ props }"
					>
						<v-btn
							:class="$vuetify.display.mdAndDown ? 'ma-1' : 'ma-3'"
							icon="mdi-dots-vertical"
							v-bind="props"/>
					</template>
					<v-list>
						<v-list-item
							v-bind="props"
							class="text-red-darken-3 text-uppercase"
							style="font-size: 1rem;"
							variant="text"
						>
							<v-icon icon="mdi-logout"/>
							Log Out
						</v-list-item>
					</v-list>
				</v-menu>
			</template>
			<v-card class="bg-dark">
				<v-card-title class="bg-black">
					<v-icon id="remind">mdi-alert-circle</v-icon>
					Confirm Logout
				</v-card-title>
				<v-card-text>Are you sure you want to log out?</v-card-text>
				<v-card-actions>
					<v-spacer></v-spacer>
					<v-btn
						color="green-darken-1"
						variant="text"
						@click="dialog = false"
                        :disabled="signingOut"
					>
						Go Back
					</v-btn>
					<v-btn
						color="red-darken-1"
						variant="text"
						@click="signOut"
                        :loading="signingOut"
					>
						Log Out
					</v-btn>
				</v-card-actions>
			</v-card>
		</v-dialog>
	</v-app-bar>
</template>

<script>
import $ from "jquery";

export default {
	name: "TopNav",
	data() {
		return {
			dialog: false,
            signingOut: false,
			signedOut: false
		}
	},
	methods: {
		signOut() {
            this.signingOut = true;
			$.ajax({
				url: `${this.$store.getters.appURL}/index.php`,
				type: 'POST',
				xhrFields: {
					withCredentials: true
				},
				data: {
					signOut: this.signedOut
				},
				success: (data) => {
					data = JSON.parse(data);
					this.$store.commit('auth/setUser', data.user = null);
					this.$router.push('/');
                    this.signingOut = false;
				},
				error: (error) => {
					alert(`ERROR ${error.status}: ${error.statusText}`);
                    this.signingOut = false;
				},
			})
		},
	}
}
</script>

<style scoped>
#topnav {
	background: linear-gradient(-45deg, #e73c7e, #23a6d5, #23d5ab, #e8af45);
	background-size: 200% 200%;

	text-fill-color: transparent;
	-webkit-background-clip: text;
	-webkit-text-fill-color: transparent;

	animation: shine 10s ease infinite;
}

#remind {
	animation: tilt-shaking 1s linear infinite;
}

@keyframes shine {
	0% {
		background-position: 0% 50%;
	}
	50% {
		background-position: 100% 50%;
	}
	100% {
		background-position: 0% 50%;
	}
}

@keyframes tilt-shaking {
	0% {
		transform: rotate(0deg);
	}
	25% {
		transform: rotate(10deg);
	}
	50% {
		transform: rotate(0deg);
	}
	75% {
		transform: rotate(-10deg);
	}
	100% {
		transform: rotate(0deg);
	}
}
</style>