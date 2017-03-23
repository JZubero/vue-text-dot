<template>
    <div>
        <div class="text-dot">{{pMsg}}</div>
    </div>
</template>

<script>
    export default {
        name: 'vue-text-dot',
        props: {
            line: {type: Number, default: 3},
            msg: {type: String, required: true},
            webfont: {type: Boolean, default: true}
        },
        data () {
            return {
                pMsg: this.msg,
                isDot: false
            }
        },
        mounted () {
            
            // If a webfont was used cut the string once more just to be sure
            // There is a known problem with the calculation of asnyc loaded font height
            if (this.webfont && window.webfontLoaded !== true)
                setTimeout(() => {
                    this.dot();

                    // Hack for the moment. TODO: Implement font loaded detection
                    window.webfontLoaded = true;
                }, 200);
            else this.dot();
        },
        methods: {
            getHeight: (elem) => {
                return {
                    height: Math.min(
                        elem.clientHeight,
                        elem.scrollHeight,
                        elem.offsetHeight),
                    lineHeight: Math.ceil(parseFloat(window.getComputedStyle(elem).getPropertyValue('line-height')))
                };
            },
            dot () {
                let dom = this.$el.querySelector('div');
                let {height, lineHeight} = this.getHeight(dom);
                
                while (height > lineHeight * this.line * 3) {
                    this.isDot = true
                    this.pMsg = this.pMsg.substring(0, this.pMsg.length / 2)
                    dom.innerHTML = this.pMsg
                    height = parseInt(window.getComputedStyle(dom).getPropertyValue('height'));
                }
                while (height > lineHeight * this.line) {
                    this.isDot = true
                    this.pMsg = this.pMsg.replace(/(\s)*([a-zA-Z0-9]+|\W)(\.\.\.)?$/, "...")
                    dom.innerHTML = this.pMsg
                    height = parseInt(window.getComputedStyle(dom).getPropertyValue('height'));
                }
                
                console.log('-');
                if (this.isDot) {
                    this.$emit('isDot')
                }
            },
        }
    }
</script>
