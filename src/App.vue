
<template>
    <v-container class="text-center">
        <!-- Skill sequence editor -->
        <div
            v-for="(sequenceRow, rowIndex) in sequenceRows"
            :key="rowIndex"
            class="mb-2"
        >
            <div
                v-for="(item, itemIndex) in sequenceRow"
                :key="itemIndex"
                class="d-inline-block mr-2"
            >
                <v-btn
                    color="error"
                    variant="outlined"
                    width="128"
                    @click.stop="removeSequenceItem(rowIndex, itemIndex)"
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
                        @click.stop="moveSequenceItem(rowIndex, itemIndex, -1)"
                        :disabled="itemIndex === 0"
                    >
                        <v-icon size="32">
                            mdi-arrow-left
                        </v-icon>
                    </v-btn>
                    <v-btn
                        variant="plain"
                        width="64"
                        @click.stop="moveSequenceItem(rowIndex, itemIndex, +1)"
                        :disabled="itemIndex === sequenceRows[rowIndex].length - 1"
                    >
                        <v-icon size="32">
                            mdi-arrow-right
                        </v-icon>
                    </v-btn>
                </div>
            </div>
            <div
                class="d-inline-block mr-2"
                style="vertical-align: top"
            >
                <v-btn
                    color="success"
                    variant="outlined"
                    width="128"
                    :height="36 + 8 + 128 + 8 + 48"
                    @click.stop="addSequenceItem(rowIndex)"
                >
                    <v-icon size="64">
                        mdi-plus
                    </v-icon>
                </v-btn>
            </div>
            <div
                class="d-inline-block mr-2"
                style="vertical-align: top"
            >
                <v-btn
                    color="error"
                    variant="outlined"
                    width="128"
                    :height="36 + 8 + 128 + 8 + 48"
                    @click.stop="removeSequenceRow(rowIndex)"
                >
                    <v-icon size="64">
                        mdi-trash-can-outline
                    </v-icon>
                </v-btn>
            </div>
            <div
                class="d-inline-block"
                style="vertical-align: top"
            >
                <v-btn
                    variant="plain"
                    width="64"
                    :height="(36 + 8 + 128 + 8 + 48 - 8) / 2"
                    @click.stop="moveSequenceRow(rowIndex, -1)"
                    :disabled="rowIndex === 0"
                >
                    <v-icon size="64">
                        mdi-arrow-up
                    </v-icon>
                </v-btn>
                <v-btn
                    class="d-block mt-2"
                    variant="plain"
                    width="64"
                    :height="(36 + 8 + 128 + 8 + 48 - 8) / 2"
                    @click.stop="moveSequenceRow(rowIndex, +1)"
                    :disabled="rowIndex === sequenceRows.length - 1"
                >
                    <v-icon size="64">
                        mdi-arrow-down
                    </v-icon>
                </v-btn>
            </div>
        </div>

        <div @click="addSequenceRow">
            <v-btn
                color="success"
                variant="outlined"
                width="100%"
                :max-width="128 * 8"
                :height="128"
                @click.stop="addSequenceRow()"
            >
                <v-icon size="64">
                    mdi-plus
                </v-icon>
                <div>Add row</div>
            </v-btn>
        </div>

        <!-- Skill sequence preview -->
        <h2 class="mt-16">
            Preview
        </h2>
        <canvas
            class="canvas"
            ref="canvas"
        />
        <div>
            <v-btn @click.stop="downloadSequence()">
                Download
            </v-btn>
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
            sequenceRows: [],
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
        this.addSequenceRow();
        this.addSequenceItem(0);
    },
    methods: {
        addSequenceRow() {
            this.sequenceRows.push([]);
        },
        moveSequenceRow(row, direction) {
            let neighbourSequenceRow = this.sequenceRows[row + direction];
            this.sequenceRows[row + direction] = this.sequenceRows[row];
            this.sequenceRows[row] = neighbourSequenceRow;
        },
        removeSequenceRow(row) {
            this.sequenceRows.splice(row, 1);
        },
        addSequenceItem(row) {
            this.sequenceRows[row].push({
                image: null,
                overlay: null
            });
        },
        moveSequenceItem(row, index, direction) {
            let neighbourSequenceItem = this.sequenceRows[row][index + direction];
            this.sequenceRows[row][index + direction] = this.sequenceRows[row][index];
            this.sequenceRows[row][index] = neighbourSequenceItem;
        },
        removeSequenceItem(row, index) {
            this.sequenceRows[row].splice(index, 1);
        },
        async generateSequence() {
            const itemHeight = 128;
            const rowGapHeight = 48;

            // Retrieve all images and canculate the total size of the sequence
            let processedSequenceRows = [];
            for (let rowIndex = 0; rowIndex < this.sequenceRows.length; rowIndex++) {
                let processedSequenceItems = [];

                const sequenceRow = this.sequenceRows[rowIndex];

                for (let itemIndex = 0; itemIndex < sequenceRow.length; itemIndex++) {
                    const sequenceItem = sequenceRow[itemIndex];
           
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

                processedSequenceRows.push(processedSequenceItems);
            }

            // Prepare the canvas
            const canvas = this.$refs.canvas;
            canvas.width = Math.max(...processedSequenceRows.map(processedSequenceRow => processedSequenceRow.map(item => item.width).reduce((width, itemWidth) => width + itemWidth, 0))); // Calculate how wide the largest row is
            canvas.height = processedSequenceRows.length * itemHeight + (processedSequenceRows.length - 1) * rowGapHeight;
            const canvasContext = canvas.getContext('2d');
            canvasContext.clearRect(0, 0, canvas.width, canvas.height);

            // Draw each sequence item on the canvas
            let y = 0;
            processedSequenceRows.forEach((processedSequenceRow) => {
                let x = 0;
                processedSequenceRow.forEach(processedSequenceItem => {
                    if(processedSequenceItem.image !== null) canvasContext.drawImage(processedSequenceItem.image, x, y, processedSequenceItem.width, itemHeight);
                    if(processedSequenceItem.overlay !== null) canvasContext.drawImage(processedSequenceItem.overlay, x, y, processedSequenceItem.width, itemHeight);

                    x += processedSequenceItem.width;
                });
                y += itemHeight + rowGapHeight;
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
        sequenceRows: {
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