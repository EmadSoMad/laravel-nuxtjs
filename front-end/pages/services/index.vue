<template>
    <div class="container">
        <div>
            <h1>
                Services
                ({{ total }})
            </h1>
            <p>
                We happy to serve you
            </p>
            <br/>
            <Loading v-if="loading"/>
            <Row v-else>
                <Col span="6" v-for="row in rows" :key="row.id">
                    <Card style="margin: 10px">

                        <p slot="title">
                            <NuxtLink :to="'/services/'+row.id">{{ row.name }}</NuxtLink>
                        </p>
                        <div style="text-align:center">
                            <NuxtLink :to="'/services/'+row.id">
                                <img :src="$images(row.image)" width="250">
                            </NuxtLink>
                        </div>
                    </Card>
                </Col>
            </Row>
            <template v-if="currentPage != lastPage">
                <Button type="primary" :loading="loading" @click="fetchData">
                    <span v-if="!loading">Click me!</span>
                    <span v-else>Loading...</span>
                </Button>
            </template>
        </div>
    </div>
</template>

<script>
    import Loading from '../../components/loading'

    export default {
        comments: {
            Loading
        },
        data() {
            return {
                rows: [],
                loading: false,
                lastPage: 1,
                nextUrl: '',
                currentPage: 1,
                total: 0
            }
        },
        mounted() {
            this.fetchData();
        },
        methods: {
            fetchData() {
                this.loading = true;
                let url = process.env.moduleUrl + 'services';
                if (this.nextUrl !== '' && this.nextUrl != null) {
                    url = this.nextUrl;
                }
                this.$axios.$get(url).then((res) => {
                    if (this.rows.length > 0) {
                        this.rows = [...this.rows, ...res.payload.data];
                    } else {
                        this.rows = res.payload.data;
                    }
                    this.nextUrl = res.payload.next_page_url;
                    this.lastPage = res.payload.last_page;
                    this.currentPage = res.payload.current_page;
                    this.total = res.payload.total;
                    this.loading = false;
                })
            }
        }
    }
</script>

