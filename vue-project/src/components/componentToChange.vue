<template>
    <div>
        <p> {{ msg }} </p>
    </div>
</template>

<script type="module">
export default {
    name: 'compToChange',
    props: {
        msg: {
            deafult: 'Message'
        },
    },
    data() {
        return {

        }
    }
}

</script>
