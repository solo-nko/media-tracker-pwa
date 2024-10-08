<script setup lang="ts">
import { computed, ref } from 'vue';
import EntryDialogGames from '../components/EntryDialogGames.vue';
import { appDatabase } from '../database/db.ts';
import { from, useObservable } from '@vueuse/rxjs';
import GameLog from '../types/GameLog.ts';
import { liveQuery } from 'dexie';
import TVLog from '../types/TVLog.ts';
import BookLog from '../types/BookLog.ts';
import EntryDialogTV from '../components/EntryDialogTV.vue';
import EntryDialogBooks from '../components/EntryDialogBooks.vue';

// keep an eye on this void typing. might be problematic
const currentGames = useObservable<GameLog[] | void>(
	from(
		liveQuery(() =>
			appDatabase.games
				.where('status')
				.anyOf(['Playing', 'Replaying'])
				.toArray()
				.then((promisedArray) => {
					playingGames.value = promisedArray.filter((game) => {
						return game.status == 'Playing';
					});
					replayingGames.value = promisedArray.filter((game) => {
						return game.status == 'Replaying';
					});
				})
		)
	)
);

const playingGames = ref<GameLog[]>();
const isThereGamesPlaying = computed(() => {
	if (playingGames.value) return playingGames.value.length > 0;
	else return false;
});
const replayingGames = ref<GameLog[]>();
const isThereGamesReplaying = computed(() => {
	if (replayingGames.value) return replayingGames.value.length > 0;
	else return false;
});

const currentTV = useObservable<TVLog[] | void>(
	from(
		liveQuery(() =>
			appDatabase.television
				.where('status')
				.anyOf(['Watching', 'Rewatching'])
				.toArray()
				.then((promisedArray) => {
					watchingTV.value = promisedArray.filter((tv) => {
						return tv.status == 'Watching';
					});
					rewatchingTV.value = promisedArray.filter((tv) => {
						return tv.status == 'Rewatching';
					});
				})
		)
	)
);

const watchingTV = ref<TVLog[]>();
const isThereTVWatching = computed(() => {
	if (watchingTV.value) return watchingTV.value.length > 0;
	else return false;
});
const rewatchingTV = ref<TVLog[]>();
const isThereTVRewatching = computed(() => {
	if (rewatchingTV.value) return rewatchingTV.value.length > 0;
	else return false;
});

const currentBooks = useObservable<BookLog[] | void>(
	from(
		liveQuery(() =>
			appDatabase.books
				.where('status')
				.anyOf(['Reading', 'Rereading'])
				.toArray()
				.then((promisedArray) => {
					readingBooks.value = promisedArray.filter((book) => {
						return book.status == 'Reading';
					});
					rereadingBooks.value = promisedArray.filter((book) => {
						return book.status == 'Rereading';
					});
				})
		)
	)
);

const readingBooks = ref<BookLog[]>([]);
const isThereBooksReading = computed(() => {
	if (readingBooks.value) return readingBooks.value.length > 0;
	else return false;
});
const rereadingBooks = ref<BookLog[]>([]);
const isThereBooksRereading = computed(() => {
	if (rereadingBooks.value) return rereadingBooks.value.length > 0;
	else return false;
});

const entryDetails = ref();
const showEditDialog = ref(false);
const whichDialog = ref('Game');
const dialogs = {
	Game: EntryDialogGames,
	TV: EntryDialogTV,
	Book: EntryDialogBooks
};

function editEntry(entryInfo, dialogType = 'Game') {
	whichDialog.value = dialogType;
	showEditDialog.value = true;
	entryDetails.value = entryInfo;
}
</script>

<template>
	<h1>Welcome to the Media Logger!</h1>
	<VContainer>
		<VRow justify="space-around">
			<!-- Games -->
			<VCard>
				<VCardTitle>Current Games</VCardTitle>
				<VCardSubtitle>Playing</VCardSubtitle>
				<VList>
					<template v-for="game in playingGames" :key="game.id">
						<VListItem>
							<span class="item-title">{{ game.title }}</span>
							<VIcon @click="editEntry(game, 'Game')">mdi-pencil</VIcon>
						</VListItem>
					</template>
					<VDivider v-if="isThereGamesReplaying"></VDivider>
					<VCardSubtitle v-if="isThereGamesReplaying">Replaying</VCardSubtitle>
					<template v-for="game in replayingGames" :key="game.id">
						<VListItem>
							<span class="item-title">{{ game.title }}</span>
							<VIcon @click="editEntry(game, 'Game')">mdi-pencil</VIcon>
						</VListItem>
					</template>
				</VList>
			</VCard>
			<!-- TV -->
			<VCard>
				<VCardTitle>Current Television</VCardTitle>
				<VCardSubtitle>Watching</VCardSubtitle>
				<VList>
					<template v-for="tv in watchingTV" :key="tv.id">
						<VListItem>
							<span class="item-title">{{ tv.title }}</span>
							<VIcon @click="editEntry(tv, 'TV')">mdi-pencil</VIcon>
						</VListItem>
					</template>
					<VDivider v-if="isThereTVRewatching"></VDivider>
					<VCardSubtitle v-if="isThereTVRewatching">Rewatching</VCardSubtitle>
					<template v-for="tv in rewatchingTV" :key="tv.id">
						<VListItem>
							<span class="item-title">{{ tv.title }}</span>
							<VIcon @click="editEntry(tv, 'TV')">mdi-pencil</VIcon>
						</VListItem>
					</template>
				</VList>
			</VCard>
			<!-- Books -->
			<VCard>
				<VCardTitle>Current Books</VCardTitle>
				<VCardSubtitle>Reading</VCardSubtitle>
				<VList>
					<template v-for="book in readingBooks" :key="book.id">
						<VListItem>
							<span class="item-title">{{ book.title }}</span>
						</VListItem>
					</template>
					<VDivider v-if="isThereBooksRereading"></VDivider>
					<VCardSubtitle v-if="isThereBooksRereading">Rereading</VCardSubtitle>
					<template v-for="book in rereadingBooks" :key="book.id">
						<VListItem>
							<span class="item-title">{{ book.title }}</span>
						</VListItem>
					</template>
				</VList>
			</VCard>
		</VRow>
		<VDialog v-model="showEditDialog">
			<!-- Because there are multiple different types of edit dialogs and only one needs to be rendered at a time, we use a dynamic component -->
			<Component
				:is="dialogs[whichDialog]"
				@close-entry="showEditDialog = false"
				:entry="entryDetails"
				:edit-entry="true"
			></Component>
		</VDialog>
	</VContainer>
</template>

<style scoped>
.item-title {
	font-weight: bold;
}
</style>
