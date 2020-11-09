<template>
<ul>
    <li v-for="item in items" :key="item.path">
        <router-link :to="item.path">{{item.title || item.path}}</router-link>
        <ul v-if="item.headers && header">
            <li v-for="header in item.headers" :key="header.slug">
                <router-link :to="item.path + '#' + header.slug">{{header.title}}</router-link>
            </li>
        </ul>
        <child-table-of-contents
            v-if="!isMax()"
            :page-url="item.regularPath"
            :header="header"
            :max="max"
            :level="nextLevel()"
        />
    </li>
</ul>
</template>

<script>

export default {
    name: "ChildTableOfContents",
    props: {
        header: {
            type: Boolean,
            default: false
        },
        pageUrl: {
            type: String,
            default: undefined
        },
        max: {
            type: Number,
            default: undefined
        },
        level: {
            type: Number,
            default: undefined
        }
    },
    computed: {
        items() {
            const currentUrl = (this.pageUrl || this.$page.regularPath);

            return this.itemChilds(currentUrl);
        }
    },
    methods: {
        allChilds() {
            return this.$site.pages
                .sort((a, b) => {
                    const aOrder = a.frontmatter && a.frontmatter.order;
                    const bOrder = b.frontmatter && b.frontmatter.order;

                    if (aOrder && bOrder) {
                        return aOrder > bOrder ? 1 : -1;
                    }

                    return 0;
                })
        },
        itemChilds(currentUrl) {
            return this.allChilds().filter(p => {
                return p.regularPath.startsWith(currentUrl) &&
                    p.regularPath !== currentUrl &&
                    p.regularPath.endsWith("/") &&
                    p.regularPath.substr(currentUrl.length).split("/").length === 2;
            });
        },
        nextLevel() {
            if (this.max === undefined) {
                return undefined;
            }

            const number = this.level === undefined ? 1 : this.level;

            return number + 1;
        },
        isMax() {
            console.log("here>" + this.max + " - " + this.level)
            if (this.max === undefined) {
                return false;
            }

            if (this.max === 1 && this.level === undefined) {
                return true;
            }

            return this.max <= this.level;
        }
    },
}
</script>
