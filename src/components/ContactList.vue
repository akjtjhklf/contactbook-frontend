<script>
    export default {
        props: {
            contacts: { type: Array, default: []},
            activeIndex: { type: Number, default: -1 },
        },
        emits: ["update:activeIndex"],
        methods: {
            updateActiveIndex(index) {
                this.$emits("update:activeIndex", index);
            }
        }
    };
</script>

<template>
    <ul class="list-group">
        <li
            class="list-group-item"
            v-for="(contact, index) in contact"
            :key="contact._id"
            :class="{ active: index === activeIndex }"
            @click="updateActiveIndex(index)"
        >
            {{ contact.name }}
        </li>
    </ul>
</template>