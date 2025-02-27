<!--
// Copyright © 2020, 2021 Anticrm Platform Contributors.
// Copyright © 2021 Hardcore Engineering Inc.
//
// Licensed under the Eclipse Public License, Version 2.0 (the "License");
// you may not use this file except in compliance with the License. You may
// obtain a copy of the License at https://www.eclipse.org/legal/epl-2.0
//
// Unless required by applicable law or agreed to in writing, software
// distributed under the License is distributed on an "AS IS" BASIS,
// WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
//
// See the License for the specific language governing permissions and
// limitations under the License.
-->
<script lang="ts">
  import type { Attachment } from '@hcengineering/attachment'
  import { PDFViewer } from '@hcengineering/presentation'
  import { showPopup, closeTooltip } from '@hcengineering/ui'
  import { ListSelectionProvider } from '@hcengineering/view-resources'
  import { createEventDispatcher } from 'svelte'

  import { getType } from '../utils'
  import AttachmentPresenter from './AttachmentPresenter.svelte'
  import AttachmentActions from './AttachmentActions.svelte'
  import AudioPlayer from './AudioPlayer.svelte'
  import { AttachmentImageSize } from '../types'
  import AttachmentImagePreview from './AttachmentImagePreview.svelte'
  import AttachmentVideoPreview from './AttachmentVideoPreview.svelte'

  export let value: Attachment
  export let isSaved: boolean = false
  export let listProvider: ListSelectionProvider | undefined = undefined
  export let imageSize: AttachmentImageSize = 'auto'
  export let removable: boolean = false

  const dispatch = createEventDispatcher()

  $: type = getType(value.type)
</script>

{#if type === 'image'}
  <!-- svelte-ignore a11y-click-events-have-key-events -->
  <!-- svelte-ignore a11y-no-static-element-interactions -->
  <div
    class="content flex-center buttonContainer cursor-pointer"
    on:click={() => {
      closeTooltip()
      if (listProvider !== undefined) listProvider.updateFocus(value)
      const popupInfo = showPopup(
        PDFViewer,
        { file: value.file, name: value.name, contentType: value.type },
        value.type.startsWith('image/') ? 'centered' : 'float'
      )
      dispatch('open', popupInfo.id)
    }}
  >
    <AttachmentImagePreview {value} size={imageSize} />
    <div class="actions conner">
      <AttachmentActions attachment={value} {isSaved} {removable} />
    </div>
  </div>
{:else if type === 'audio'}
  <div class="buttonContainer">
    <AudioPlayer {value} />
    <div class="actions conner" style:padding={'0.125rem 0.25rem'}>
      <AttachmentActions attachment={value} {isSaved} {removable} />
    </div>
  </div>
{:else if type === 'video'}
  <div class="content buttonContainer flex-center">
    <AttachmentVideoPreview {value} />
    <div class="actions conner">
      <AttachmentActions attachment={value} {isSaved} {removable} />
    </div>
  </div>
{:else}
  <div class="flex buttonContainer extraWidth">
    <AttachmentPresenter {value} />
    <div class="actions conner">
      <AttachmentActions attachment={value} {isSaved} {removable} />
    </div>
  </div>
{/if}

<style lang="scss">
  .buttonContainer {
    position: relative;
    width: fit-content;
    height: fit-content;

    &.extraWidth {
      padding-right: 2.5rem;
    }

    .actions {
      visibility: hidden;
      position: absolute;
      top: 0.25rem;
      right: 0.25rem;
      padding: 0.25rem;
      background-color: var(--theme-comp-header-color);
      border: 1px solid var(--theme-divider-color);
      border-radius: 0.75rem;

      &.conner {
        top: 0;
        right: 0;
        padding: 0.25rem;
        border-radius: 0 0.4rem 0 0.25rem;
      }
    }
  }

  .buttonContainer:hover {
    .actions {
      visibility: visible;
    }
  }

  .content {
    max-width: 20rem;
    max-height: 20rem;
  }
</style>
