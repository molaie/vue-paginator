<template>
<nav aria-label="Page navigation">
    <ul class="pagination" :class="sizeClass" v-if="this.lastPage > 0" >
        <li v-if="showPrevious" :class="{ 'disabled' : currentPage <= 1 }">
            <span v-if="currentPage <= 1">
                <span aria-hidden="true">{{ pagerOptions.previousText }}</span>
            </span>
            
            <a href="#" v-if="currentPage > 1 " :aria-label="pagerOptions.ariaPrevious" 
                @click.prevent="changePage(currentPage - 1)">
                <span aria-hidden="true">{{ pagerOptions.previousText }}</span>
            </a>
        </li>
        <li v-for="num in pagerArray" :class="{ 'active': num === currentPage }">
            <a href="#" @click.prevent="changePage(num)">{{ num }}</a>
        </li>
        <li v-if="showNext" :class="{ 'disabled' : currentPage === this.lastPage }">
            <span v-if="currentPage === this.lastPage || this.lastPage === 0">
                <span aria-hidden="true">{{ pagerOptions.nextText }}</span>
            </span>
            <a href="#" v-if="currentPage < this.lastPage" :aria-label="pagerOptions.ariaNext" 
            @click.prevent="changePage(currentPage + 1)">
                <span aria-hidden="true">{{ pagerOptions.nextText }}</span>
            </a>
        </li>
    </ul>
</nav>


</template>

<script>
    export default {
        data: function() {
            return {
                pagerOptions: {
                    ariaPrevious: 'Previous',
                    ariaNext: 'Next',
                    previousText: '«',
                    nextText: '»',
                    alwaysShowPrevNext: false
                },
                lastPage: 1
            }
        },
        props: {
            options: {
                type: Object
            },
            size: {
                type: String
            },
            currentPage: {
                type: Number,
                default: 1
            },
            perPage: {
                type: Number,
                default: 15
            },
            total: {
                type: Number,
                default: 0
            },
            offset: {
                type: Number,
                default: 4
            }
        },
        created: function() {
            Object.assign(this.pagerOptions, this.options);
        },
        computed: {
            pagerarray: function() {
                var pagesCount = Math.ceil(this.total / this.perPage);
                let from = this.currentPage - this.offset;
                if (from < 1) {
                    from = 1;
                }

                let to = this.currentPage + this.offset;
                if (to > pagesCount) {
                    to = pagesCount;
                }
                this.lastPage = to;
                let arr = [];
                while (from <= to) {
                    arr.push(from);
                    from++;
                }
                return arr;
            },
            sizeClass() {
                if (this.size === 'large') {
                    return 'pagination-lg';
                } else if (this.size === 'small') {
                    return 'pagination-sm';
                }
                return '';
            }
        },
        methods: {
            showPrevious: function() {
                return this.pagerOptions.alwaysShowPrevNext || this.currentPage > 1;
            },
            showNext: function() {
                return this.pagerOptions.alwaysShowPrevNext || this.currentPage < this.lastPage;
            },
            changePage: function(page) {
                if (this.currentPage === page) {
                    return;
                }
                //let parent knows of change
                this.$emit('pagechanged', page);
            }
        }
    };
</script>