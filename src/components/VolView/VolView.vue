<template>
    <div
        id="volView"
        :class="$store.getters.view === 'volView' ?
            'volViewShow' : 'volViewHidden'"
        style="z-index: -2;"
    >
        <template v-if="$store.state.view.vol">
            <div
                id="volViewInfo"
                :style="{
                    backgroundColor: $store.state.view.vol.color ?
                        `rgba(${$store.state.view.vol.color[0]}, ${$store.state.view.vol.color[1]}, ${$store.state.view.vol.color[2]}, 0.35)` :
                        'rgba(0, 0, 0, 0.35)'}"
            >
                <div
                    id="volViewCover"
                    :style="{ backgroundImage: `url(${$store.state.view.vol.cover})`
            }"></div>
                <div id="volViewIntro">
                    <div id="volViewIntroContainer">
                        <div id="volViewOperate">
                            <div>
                                <img
                                    id="volViewOperateLike"
                                    :src="`../pic/${isThisLiked ? 'liked' : 'like'}.svg`"
                                    @click.stop="like"
                                />
                                <img
                                    id="volViewToggle"
                                    :src="isThisPlaying ?
                                        '../pic/controller-pause.svg' :
                                        '../pic/controller-play.svg'"
                                    v-on:click.stop="play"
                                />
                            </div>
                            <p><span v-for="tag in $store.state.view.vol.tag">
                                &nbsp;&nbsp;&nbsp;&nbsp;{{ tag }}
                            </span></p>
                        </div>
                        <p id="volViewIntroTitle">{{ $store.state.view.vol.title }}</p>
                        <p
                            id="volViewIntroDesc"
                            v-html="$store.state.view.vol.description.slice(4)"
                        />
                        <p id="volViewIntroDate">
                            <img :src="'../pic/logo.png'"/>
                            落在低处・{{ $store.state.view.vol.date }}
                        </p>
                    </div>
                </div>
            </div>
            <div id="tracks">
                <VolTrack
                    v-for="(track, index) in $store.state.view.vol.tracks"
                    :data="Object.freeze(track)"
                    :type="$store.state.view.vol.type"
                    :key="`${track.track_id}-${index}`"
                    :index="index"
                    :remote="remote"
                />
            </div>
        </template>
    </div>
</template>


<script>
    import Vue from 'vue';
    import VolTrack from './VolTrack.vue';


    export default {
        name: 'volView',
        props: ['remote'],
        components: { VolTrack },
        methods: {
            play: function () {
                const state = this.$store.state;
                state.play.vol &&
                state.play.type === state.view.vol.type &&
                state.play.vol.vol === state.view.vol.vol ?
                    this.$store.dispatch('toggle', 'play') :
                    this.$store.dispatch('play', {
                        type: state.view.vol.type,
                        data: Object.freeze(state.view.vol),
                        index: 0
                    })
            },
            like: function () {
                this.$store.dispatch('like', {
                    type: 'vol',
                    data: {
                        id: this.$store.state.view.vol.vol_id,
                        vol: this.$store.state.view.vol.vol,
                        liked: !this.isThisLiked
                    },
                    remote: this.remote
                })
            }
        },
        computed: {
            isThisPlaying: function () {
                const state = this.$store.state;
                return state.play.playing && state.play.vol &&
                    state.play.type === state.view.vol.type &&
                    state.play.vol.vol === state.view.vol.vol
            },
            isThisLiked : function () {
                return this.$store.state.user.likedVols.includes(this.$store.state.view.vol.vol)
            }
        }
    }
</script>


<style lang="sass" scoped>
    #volView
        position: fixed
        width: 90%
        height: calc(100% - 180px)
        top: 80px
        left: 0
        padding: 25px 5% 0 5%
        overflow-y: auto

    .volViewShow
        transform: scale(1)
        opacity: 1
        transition: all ease 500ms 350ms

    .volViewHidden
        transform: scale(0.9)
        opacity: 0
        transition: all ease 500ms 0ms

    #volViewInfo
        width: 100%
        height: 0
        padding-bottom: 31.8%
        display: flex
        flex-direction: row
        justify-content: space-between
        text-align: left
        box-shadow: 0 10px 50px 0 rgba(0,0,0,0.50)
        overflow: hidden

    #volViewCover
        width: 44%
        height: 0
        padding-bottom: 31.8%
        background-size: cover
        box-shadow: 10px 0 50px 0 rgba(0,0,0,0.50)

    #volViewIntro
        width: 48%
        height: 0
        padding-bottom: 31.8%
        padding-right: 3%
        overflow-y: auto

    #volViewIntroContainer
        margin-bottom: -100%

    #volViewIntroTitle
        font-size: 1.5em
        margin: 10px 0

    #volViewIntroDesc
        font-size: 0.8em
        margin-bottom: 15px
        line-height: 1.49em

    #volViewIntroDate
        font-size: 0.7em
        font-weight: 400
        margin-bottom: 25px

        & > img
            width: 3%
            height: auto
            position: relative
            top: 3px
            margin-right: 2px

    #volViewOperate
        margin-top: 25px
        display: flex
        flex-direction: row
        justify-content: space-between
        align-items: center

        p
            font-size: 0.8em

        & > div
            width: 40%

        img
            width: 10%
            height: auto
            cursor: pointer
            transition: all ease 300ms
            opacity: 0.8

            &:hover
                transform: scale(1)
                opacity: 1

        #volViewOperateLike
            margin-right: 15px
            width: 11.4%

    #tracks
        width: 100%
        height: auto
        margin: 30px 0
        display: flex
        flex-direction: row
        flex-wrap: wrap

</style>