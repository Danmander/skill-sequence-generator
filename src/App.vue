
<template>
    <v-container class="text-center">
        <!-- Skill sequence preview -->
        <canvas
            class="canvas"
            ref="canvas"
        />
        <div class="mb-16">
            <v-btn @click="downloadSequence()">
                Download
            </v-btn>
        </div>

        <!-- Skill sequence editor -->
        <div>
            <div
                v-for="(item, index) in sequenceItems"
                :key="index"
                class="d-inline-block mr-2"
            >
                <v-btn
                    color="error"
                    variant="outlined"
                    width="128"
                    @click="removeSequenceItem(index)"
                >
                    <v-icon size="32">
                        mdi-trash-can-outline
                    </v-icon>
                </v-btn>
                <sequence-item
                    class="mt-2"
                    v-model="item.image"
                />
                <v-autocomplete
                    class="mt-2"
                    v-model="item.overlay"
                    variant="outlined"
                    density="comfortable"
                    hide-details
                    :items="overlays"
                    item-title="label"
                    item-value="image"
                />
                <div
                    class="mt-2"
                >
                    <v-btn
                        variant="plain"
                        width="64"
                        @click="moveSequenceItem(index, -1)"
                        :disabled="index === 0"
                    >
                        <v-icon size="32">
                            mdi-arrow-left
                        </v-icon>
                    </v-btn>
                    <v-btn
                        variant="plain"
                        width="64"
                        @click="moveSequenceItem(index, +1)"
                        :disabled="index === sequenceItems.length - 1"
                    >
                        <v-icon size="32">
                            mdi-arrow-right
                        </v-icon>
                    </v-btn>
                </div>
            </div>
            <div
                class="d-inline-block"
                style="vertical-align: top"
            >
                <v-btn
                    color="success"
                    variant="outlined"
                    width="128"
                    :height="36 + 8 + 128 + 8 + 48"
                    @click="addSequenceItem()"
                >
                    <v-icon size="64">
                        mdi-plus
                    </v-icon>
                </v-btn>
            </div>
        </div>
    </v-container>
</template>

<script>
import SequenceItem from './components/SequenceItem.vue';
import SkillOverlay1 from '@/assets/images/1.png';
import SkillOverlay2 from '@/assets/images/2.png';
import SkillOverlay3 from '@/assets/images/3.png';
import SkillOverlay4 from '@/assets/images/4.png';
import SkillOverlay5 from '@/assets/images/5.png';
import SkillOverlayF1 from '@/assets/images/F1.png';
import SkillOverlayF2 from '@/assets/images/F2.png';
import SkillOverlayF3 from '@/assets/images/F3.png';
import SkillOverlayF4 from '@/assets/images/F4.png';
import SkillOverlayF5 from '@/assets/images/F5.png';
import SkillOverlayHeal from '@/assets/images/Heal.png';
import SkillOverlayElite from '@/assets/images/Elite.png';
import SkillOverlayPlus64 from '@/assets/images/Plus64.png';
import SkillOverlayPlus96 from '@/assets/images/Plus96.png';
import SkillOverlayPlus128 from '@/assets/images/Plus128.png';
import SkillOverlayArrow96 from '@/assets/images/Arrow96.png';
import SkillOverlayArrow128 from '@/assets/images/Arrow128.png';
import SkillOverlayWhen from '@/assets/images/When.png';

export default {
    components: {
        SequenceItem,
    },
    data() {
        return {
            sequenceItems: [],
            overlays: [
                {
                    label: "",
                    image: null,
                },
                {
                    label: "1",
                    image: SkillOverlay1,
                },
                {
                    label: "2",
                    image: SkillOverlay2,
                },
                {
                    label: "3",
                    image: SkillOverlay3,
                },
                {
                    label: "4",
                    image: SkillOverlay4,
                },
                {
                    label: "5",
                    image: SkillOverlay5,
                },
                {
                    label: "F1",
                    image: SkillOverlayF1,
                },
                {
                    label: "F2",
                    image: SkillOverlayF2,
                },
                {
                    label: "F3",
                    image: SkillOverlayF3,
                },
                {
                    label: "F4",
                    image: SkillOverlayF4,
                },
                {
                    label: "F5",
                    image: SkillOverlayF5,
                },
                {
                    label: "Heal",
                    image: SkillOverlayHeal,
                },
                {
                    label: "Elite",
                    image: SkillOverlayElite,
                },
                {
                    label: "+ (64)",
                    image: SkillOverlayPlus64,
                },
                {
                    label: "+ (96)",
                    image: SkillOverlayPlus96,
                },
                {
                    label: "+ (128)",
                    image: SkillOverlayPlus128,
                },
                {
                    label: "-> (96)",
                    image: SkillOverlayArrow96,
                },
                {
                    label: "-> (128)",
                    image: SkillOverlayArrow128,
                },
                {
                    label: "when (192)",
                    image: SkillOverlayWhen,
                },
            ]
        }
    },
    mounted() {
        this.addSequenceItem();
    },
    methods: {
        addSequenceItem() {
            this.sequenceItems.push({
                image: null,
                overlay: null
            });
        },
        moveSequenceItem(index, direction) {
            let neighbourSequenceItem = this.sequenceItems[index + direction];
            this.sequenceItems[index + direction] = this.sequenceItems[index];
            this.sequenceItems[index] = neighbourSequenceItem;
        },
        removeSequenceItem(index) {
            this.sequenceItems.splice(index, 1);
        },
        async generateSequence() {
            const itemHeight = 128;

            // Retrieve all images and canculate the total size of the sequence
            let processedSequenceItems = [];
            for (let index = 0; index < this.sequenceItems.length; index++) {
                const sequenceItem = this.sequenceItems[index];
           
                let processedSequenceItem = {
                    image: null,
                    overlay: null,
                    width: null
                };

                // Retrieve the images
                if(sequenceItem.image !== null) processedSequenceItem.image = await this.createImage(sequenceItem.image);
                if(sequenceItem.overlay !== null) processedSequenceItem.overlay = await this.createImage(sequenceItem.overlay);

                // Calculate the proper size of the image + overlay
                if(processedSequenceItem.image === null && processedSequenceItem.overlay == null) processedSequenceItem.width = 128; // Default to being an open space if there is neither an image nor overlay
                else if(processedSequenceItem.image === null) processedSequenceItem.width = processedSequenceItem.overlay.width;
                else if(processedSequenceItem.overlay === null) processedSequenceItem.width = processedSequenceItem.image.width;
                else processedSequenceItem.width = Math.max(processedSequenceItem.image.width, processedSequenceItem.overlay.width);

                processedSequenceItems.push(processedSequenceItem);
            }

            // Prepare the canvas
            const canvas = this.$refs.canvas;
            canvas.width = processedSequenceItems.map(item => item.width).reduce((width, itemWidth) => width + itemWidth, 0);
            canvas.height = itemHeight;
            const canvasContext = canvas.getContext('2d');
            canvasContext.clearRect(0, 0, canvas.width, canvas.height);

            // Draw each sequence item on the canvas
            let x = 0;
            processedSequenceItems.forEach(async (processedSequenceItem) => {
                if(processedSequenceItem.image !== null) canvasContext.drawImage(processedSequenceItem.image, x, 0, processedSequenceItem.width, itemHeight);
                if(processedSequenceItem.overlay !== null) canvasContext.drawImage(processedSequenceItem.overlay, x, 0, processedSequenceItem.width, itemHeight);

                x += processedSequenceItem.width;
            });
        },
        createImage(fileOrUrl) {
            return new Promise((resolve, reject) => {
                const image = new Image();
                image.onload = () => resolve(image);
                image.onerror = () => reject();
                image.src = fileOrUrl instanceof File ? URL.createObjectURL(fileOrUrl) : fileOrUrl;
            });
        },
        downloadSequence() {
            const link = document.createElement('a');
            link.download = `sequence-${this.getDateTimeString()}.png`;
            link.href = this.$refs.canvas.toDataURL('image/png');
            link.click();
            link.remove();
        },
        getDateTimeString() {
            const now = new Date();
            return now.getFullYear() + '-' + String(now.getMonth() + 1).padStart(2, '0') + '-' + String(now.getDate()).padStart(2, '0') + '-' + String(now.getHours()).padStart(2, '0') + '-' + String(now.getMinutes()).padStart(2, '0') + '-' + String(now.getSeconds()).padStart(2, '0');
        }
    },
    watch: {
        sequenceItems: {
            deep: true,
            handler() {
                this.generateSequence();
            }
        }
    }
}
</script>

<style scoped>
.canvas {
    background-color: rgb(var(--v-theme-surface));
}
</style>