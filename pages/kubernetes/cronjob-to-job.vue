<script setup lang="ts">
import { parse } from 'yaml';

definePageMeta({
    utilityGroup: "Kubernetes",
    utilityTitle: "CronJob to Job",
    utilityDescription: "Convert a Kubernetes CronJob to a Job",
})

const cronJob = ref("")

const resource = computed(() => {
    const res = parse(cronJob.value) || {}
    delete (res.spec?.jobTemplate?.spec.template.metadata)
    return {
        apiVersion: 'batch/v1',
        kind: 'Job',
        metadata: {
            name: (res.metadata?.name ?? 'job') + '-once',
        },
        spec: {
            template: {
                spec: res.spec?.jobTemplate?.spec?.template?.spec ?? {},
            }
        }
    }
})
</script>


<template>
    <PageWithSidebar>
        <template #sidebar>
            <div class="grid grid-cols-1 gap-4">
                <UFormGroup label="CronJob YAML">
                    <UTextarea v-model="cronJob" rows="16"></UTextarea>
                </UFormGroup>
            </div>
        </template>
        <template #default>
            <ResourceOutput :resource="resource"></ResourceOutput>
        </template>
    </PageWithSidebar>
</template>