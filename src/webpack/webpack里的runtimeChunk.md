# webpack里的runtimeChunk
老版本的Chunk.entry已废弃，使用过hasRuntime来判断

```
hasRuntime() {
		for (const chunkGroup of this._groups) {
			if (
				chunkGroup.isInitial() &&
				chunkGroup instanceof Entrypoint &&
				chunkGroup.getRuntimeChunk() === this
			) {
				return true;
			}
		}
		return false;
	}
```