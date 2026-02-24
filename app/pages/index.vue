<script lang="ts" setup>
import { SafeArea, SystemBarsType } from "@capacitor-community/safe-area";

const { isElectron, isMobile, isWeb } = usePlatform();

const currentView = ref<string>("");

watch(currentView, async () => {
    await nextTick();
    updateSystemBars();
});

const updateSystemBars = async () => {
    if (!isMobile.value) return;

    try {
        const isLandscape = window.innerWidth > window.innerHeight;

        if (isLandscape) {
            await SafeArea.hideSystemBars({ type: SystemBarsType.StatusBar });
            await SafeArea.hideSystemBars({
                type: SystemBarsType.NavigationBar,
            });
        } else {
            await SafeArea.showSystemBars({ type: SystemBarsType.StatusBar });
            await SafeArea.hideSystemBars({
                type: SystemBarsType.NavigationBar,
            });
        }
    } catch (e) {
        console.error("Bars update failed", e);
    }
};

onMounted(() => {
    setTimeout(() => {
        updateSystemBars();
    }, 500);
    window.addEventListener("resize", updateSystemBars);

    if (isWeb.value) {
        currentView.value = "chooseGame";
    } else if (isElectron.value) {
        currentView.value = "desktopHome";
    } else if (isMobile.value) {
        currentView.value = "mobileHome";
    }
});

onUnmounted(() => {
    window.removeEventListener("resize", updateSystemBars);
});

const launchMap = () => {
    currentView.value = "map";
};

const launchChooseGame = () => {
    currentView.value = "chooseGame";
};

const goToDesktopIndex = () => {
    currentView.value = "desktopHome";
};

const goHome = () => {
    if (isElectron.value) currentView.value = "desktopHome";
    if (isMobile.value) currentView.value = "mobileHome";
};
</script>

<template>
    <Transition name="page-fade">
        <DesktopIndex
            v-if="currentView === 'desktopHome'"
            :launch-choose-game="launchChooseGame"
        />
    </Transition>

    <Transition name="page-fade">
        <ChooseGame
            v-if="currentView === 'chooseGame'"
            :launch-map="launchMap"
            :go-to-desktop-index="goToDesktopIndex"
        />
    </Transition>

    <Transition name="page-fade">
        <MobileIndex
            v-if="currentView === 'mobileHome'"
            @connected="currentView = 'map'"
        />
    </Transition>

    <Transition name="page-fade">
        <LazyMap v-if="currentView === 'map'" :goHome="goHome" />
    </Transition>
</template>
