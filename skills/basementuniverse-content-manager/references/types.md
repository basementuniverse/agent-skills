# Type Reference

## Enums

### ContentItemType

- json
- font
- image
- audio
- text

### ContentManagerStatus

- idle
- loading
- processing
- ready

### ContentItemStatus

- idle
- loading
- loaded
- processing
- processed

## Core Shapes

### ContentManagerOptions

```ts
type ContentManagerOptions = {
  loaders: ContentLoaderMap;
  processors?: ContentProcessorMap;
  simulateSlowLoading?: boolean;
  slowLoadingTimeMin?: number;
  slowLoadingTimeMax?: number;
  simulateSlowProcessing?: boolean;
  slowProcessingTimeMin?: number;
  slowProcessingTimeMax?: number;
  throwOnNotFound?: boolean;
};
```

### ContentListItem

```ts
type ContentListItem = {
  name: string;
  type: ContentItemType | string;
  args: any[];
  processors?: ContentProcessorListItem[];
};
```

### ContentProcessorListItem

```ts
type ContentProcessorListItem = {
  name: string;
  args?: any[];
};
```

### ContentItem<T = any>

```ts
type ContentItem<T = any> = {
  name: string;
  type: ContentItemType | string;
  content: T;
  status: ContentItemStatus;
};
```

## Function Contracts

### ContentLoader

```ts
type ContentLoader = <T>(...args: any[]) => Promise<T>;
```

### ContentProcessor

```ts
type ContentProcessor = <T = any>(
  content: Record<string, ContentItem>,
  item: ContentItem<T>,
  ...args: any[]
) => Promise<void>;
```

### ContentLoaderMap

```ts
type ContentLoaderMap = {
  [key in ContentItemType | string]: ContentLoader;
};
```

### ContentProcessorMap

```ts
type ContentProcessorMap = {
  [key: string]: ContentProcessor;
};
```

## Processor-Specific Types

### ImageNameProcessorOptions

```ts
type ImageNameProcessorOptions = {
  imageNameFieldName: string;
  imageFieldName: string;
};
```

Defaults:
- imageNameFieldName: imageName
- imageFieldName: image
