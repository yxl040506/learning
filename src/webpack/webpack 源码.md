// MultiEntryPlugin.js

compiler.hooks.make.tapAsync(
			"MultiEntryPlugin",
			(compilation, callback) => {
				const { context, entries, name } = this;

				const dep = MultiEntryPlugin.createDependency(entries, name);
				compilation.addEntry(context, dep, name, callback);
			}
		);
		

	
// compiler.js		
compile(callback) {
		const params = this.newCompilationParams();
		this.hooks.beforeCompile.callAsync(params, err => {
			if (err) return callback(err);

			this.hooks.compile.call(params);

			const compilation = this.newCompilation(params);

			this.hooks.make.callAsync(compilation, err => {
				if (err) return callback(err);

				compilation.finish(err => {
					if (err) return callback(err);

					compilation.seal(err => {
						if (err) return callback(err);

						this.hooks.afterCompile.callAsync(compilation, err => {
							if (err) return callback(err);

							return callback(null, compilation);
						});
					});
				});
			});
		});
	}
	
// Compiler.js	

run
hooks.beforeRun
hooks.run
this.compile
hooks.compile
newCompilation
hooks.make

// Compilation.js

addEntry
_addModuleChain
addModule
buildModule
this.hooks.buildModule
multiModule.build
afterBuild
processModuleDependencies
addDependenciesBlock(module);
addDependency(SingleEntryDependency) // dependencies.set(factory, (innerMap = new Map()));
addModuleDependencies

forEach dependency

// NormalModuleFactory.js

create()
hooks.beforeResolve

this.hooks.factory.tap("NormalModuleFactory", () => (result, callback) => {
