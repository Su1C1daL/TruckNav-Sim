<script lang="ts" setup>
const props = defineProps<{
    launchMap: () => void;
    goToDesktopIndex: () => void;
}>();
const { selectedGame, commitSelection } = useGameSelection();
const { isElectron } = usePlatform();

const handleStart = () => {
    commitSelection();
    props.launchMap();
};
</script>

<template>
    <div class="choose-game-section">
        <div class="top-tagline">
            <button
                v-show="isElectron"
                @click="goToDesktopIndex"
                class="back-btn"
            >
                <Icon name="material-symbols:arrow-back-rounded" size="22" />
            </button>

            <Icon name="material-symbols:globe" class="icon" size="22" />
            <span>Select Game</span>
        </div>

        <div class="game-selection">
            <GameSelection v-model="selectedGame" :width="950" />
        </div>

        <button
            :disabled="!selectedGame"
            @click.prevent="handleStart"
            class="btn nav-btn"
        >
            <span>Start Navigation</span>
            <Icon name="material-symbols:map-rounded" size="20" />
        </button>
    </div>
</template>

<style
    lang="scss"
    scoped
    src="~/assets/scss/scoped/Layouts/chooseGame.scss"
></style>
