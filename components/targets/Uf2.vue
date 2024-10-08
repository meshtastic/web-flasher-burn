<template>
    <div class="relative p-4 w-full max-w-4xl max-h-full">
        <div class="relative bg-white rounded-lg shadow dark:bg-black">
            <FlashHeader />
            <div class="p-4 md:p-5">
                <ReleaseNotes />
                <ol v-if="firmwareStore.canShowFlash" class="relative border-s border-gray-200 dark:border-gray-600 ms-3.5 mb-4 md:mb-5">
                    <li class="mb-10 ms-8">
                        <span class="absolute flex items-center justify-center w-6 h-6 bg-purple-100 rounded-full -start-3 ring-8 ring-white dark:ring-gray-900 dark:bg-purple-900">
                            1
                        </span>
                        <h3 class="flex items-start mb-1 text-lg font-semibold text-gray-900 dark:text-white">
                            Enter (UF2) DFU Mode
                        </h3>
                        <div class="p-4 mb-4 my-2 text-sm text-purple-800 rounded-lg bg-purple-50 dark:bg-gray-800 dark:text-purple-400" role="alert">
                            <span class="font-medium">
                                <InformationCircleIcon class="h-4 w-4 inline" />
                                For firmware versions &lt; {{ deviceStore.enterDfuVersion }}, trigger DFU mode manually by {{ deviceStore.dfuStepAction }}
                            </span>
                        </div>
                        <button type="button"
                            class="inline-flex items-center py-2 px-3 text-sm font-medium focus:outline-none bg-meshtastic rounded-lg hover:bg-white focus:z-10 focus:ring-4 focus:ring-gray-200 text-black"
                            @click="deviceStore.enterDfuMode()">
                            <FolderArrowDownIcon class="h-4 w-4 text-black" />
                            Enter DFU Mode
                        </button>
                    </li>
                    <li class="mb-10 ms-8">
                        <span class="absolute flex items-center justify-center w-6 h-6 bg-purple-100 rounded-full -start-3 ring-8 ring-white dark:ring-gray-900 dark:bg-purple-900">
                            2
                        </span>
                        <h3 class="flex items-start mb-1 text-lg font-semibold text-gray-900 dark:text-white">
                            Ensure device DFU mode drive is mounted
                        </h3>
                        <span>
                            The drive may have a different name depending on your device hardware and its bootloader.
                        </span>
                        <div>
                            <img v-if="deviceStore.isSelectedNrf" src="@/assets/img/dfu.png" alt="DFU Drive" />
                            <img v-else src="@/assets/img/uf2_rp2040.png" alt="DFU Drive" />
                        </div>
                    </li>
                    <li class="ms-8">
                        <span class="absolute flex items-center justify-center w-6 h-6 bg-purple-100 rounded-full -start-3 ring-8 ring-white dark:ring-gray-900 dark:bg-purple-900">
                            3
                        </span>
                        <h3 class="mb-1 text-lg font-semibold text-gray-900 dark:text-white">
                            Download UF2 file to DFU drive
                        </h3>
                        <span>
                            Download and Copy UF2 file to the DFU drive.
                            Firmware should be flashed after the file is downloaded and the device reboots.
                        </span>
                    </li>
                </ol>
                <div v-if="firmwareStore.canShowFlash">
                    <a :href="downloadUf2FileUrl" v-if="firmwareStore.selectedFirmware?.id" 
                    class="text-black inline-flex w-full justify-center bg-meshtastic hover:bg-gray-200 focus:ring-4 focus:outline-none focus:ring-purple-300 font-medium rounded-lg text-sm px-5 py-2.5 text-center">
                    Download UF2
                    </a>
                    <button @click="downloadUf2FileFs" v-else
                        class="text-black inline-flex w-full justify-center bg-meshtastic hover:bg-gray-200 focus:ring-4 focus:outline-none focus:ring-purple-300 font-medium rounded-lg text-sm px-5 py-2.5 text-center">
                        Download UF2
                    </button>
                </div>
            </div>
        </div>
    </div>
</template>

<script lang="ts" setup>
import {
  FolderArrowDownIcon,
  InformationCircleIcon,
} from '@heroicons/vue/24/solid';

import { useDeviceStore } from '../../stores/deviceStore';
import { useFirmwareStore } from '../../stores/firmwareStore';
import FlashHeader from './FlashHeader.vue';
import ReleaseNotes from './ReleaseNotes.vue';

const deviceStore = useDeviceStore();
const firmwareStore = useFirmwareStore();

const downloadUf2FileFs = () => {
    const searchRegex = new RegExp(`firmware-${deviceStore.$state.selectedTarget.platformioTarget}-.+.uf2`);
    firmwareStore.downloadUf2FileSystem(searchRegex);
}

const downloadUf2FileUrl = computed(() => {
    if (!firmwareStore.selectedFirmware?.id) return '';
    const firmwareVersion = firmwareStore.selectedFirmware.id.replace('v', '')
    const firmwareFile = `firmware-${deviceStore.$state.selectedTarget.platformioTarget}-${firmwareVersion}.uf2`
    return firmwareStore.getReleaseFileUrl(firmwareFile);
});
</script>