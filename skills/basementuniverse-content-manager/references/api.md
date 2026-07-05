# @basementuniverse/content-manager API

## Main Export

Default export:
- ContentManager (static singleton class)

Named exports:
- Types: ContentManagerOptions, ContentListItem, ContentItem, ContentLoader, ContentLoaderMap, ContentProcessor, ContentProcessorMap, ContentProcessorListItem
- Enums: ContentItemType, ContentManagerStatus, ContentItemStatus
- Loaders: AudioLoader, FontLoader, ImageLoader, JSONLoader, TextLoader
- Processors: ImageNameProcessor

## ContentManager Methods

### initialise(options?: Partial<ContentManagerOptions>): void

Creates the singleton instance. Throws if already initialised.

### dispose(): void

Disposes singleton instance. Throws if not initialised.

### load(items?: ContentListItem[]): Promise<void>

Loads all content items, then runs processors.
- Returns early when items is undefined or empty.
- Throws if no loaders configured.
- Throws if a content type has no matching loader.
- Throws if processors are requested but missing/undefined.
- Throws if a processor name is not registered.

### get<T>(name: string): T | undefined

Retrieves previously loaded content by name.
- Returns undefined when not found unless throwOnNotFound is true.
- Throws when not initialised.

## ContentManager Getters

### progress: number

Unit scalar in [0, 1].
- Computed as currentProgress / currentTotalProgress.
- Returns 1 when no current list/total progress exists.

### status: ContentManagerStatus

Current lifecycle status:
- idle
- loading
- processing
- ready

## Default Loader Registry

- json -> JSONLoader
- font -> FontLoader
- image -> ImageLoader
- audio -> AudioLoader
- text -> TextLoader

## Default Processor Registry

- imageName -> ImageNameProcessor
