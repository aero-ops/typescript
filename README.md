# @aero-ops/typescript

Configuração base de TypeScript compartilhada pelos projetos do monorepo AeroOps.

Este pacote não contém código de aplicação. Ele publica `tsconfig.base.json` para que projetos TypeScript possam estender uma configuração comum, padronizada e compatível com Bun.

> Este é um pacote interno da AeroOps. Contribuições externas não são aceitas.

## Uso

Em um projeto com acesso ao registro interno da AeroOps, instale o pacote como dependência de desenvolvimento:

```bash
bun add --dev @aero-ops/typescript
```

Depois, estenda a configuração no `tsconfig.json` do projeto:

```json
{
  "extends": ["@aero-ops/typescript/base.json"],
  "include": ["src/**/*.ts"]
}
```

Opções específicas do projeto podem ser adicionadas em `compilerOptions` e têm precedência sobre as opções da configuração base.

Como a configuração define `"types": ["bun"]`, os tipos do Bun precisam estar disponíveis no projeto consumidor. Instale `@types/bun` como dependência de desenvolvimento quando necessário.

## Configuração compartilhada

`tsconfig.base.json` aplica as seguintes regras:

| Opção | Valor | Finalidade |
| --- | --- | --- |
| `target` | `ESNext` | Usa os recursos mais recentes do JavaScript. |
| `module` | `ESNext` | Mantém a saída de módulos ES. |
| `moduleResolution` | `bundler` | Resolve módulos de acordo com o comportamento de bundlers. |
| `allowImportingTsExtensions` | `true` | Permite imports com extensão `.ts`. |
| `noEmit` | `true` | Impede a geração de arquivos JavaScript. |
| `incremental` | `true` | Permite cacheamento incremental de verificações. |
| `lib` | `ESNext` | Habilita as bibliotecas padrão mais recentes. |
| `verbatimModuleSyntax` | `true` | Mantém imports e exports alinhados à sintaxe usada no código. |
| `strict` | `true` | Ativa as verificações estritas do TypeScript. |
| `skipLibCheck` | `true` | Evita verificações dentro dos arquivos de declaração de dependências. |
| `resolveJsonModule` | `true` | Permite importar arquivos JSON. |
| `experimentalDecorators` | `true` | Habilita decorators experimentais. |
| `emitDecoratorMetadata` | `true` | Gera metadados de decorators quando houver emissão. |
| `allowSyntheticDefaultImports` | `true` | Permite imports default sintéticos. |
| `esModuleInterop` | `true` | Melhora a interoperabilidade entre módulos CommonJS e ES modules. |
| `forceConsistentCasingInFileNames` | `true` | Exige nomes de arquivos com casing consistente entre sistemas. |
| `isolatedModules` | `true` | Garante que cada arquivo possa ser transpilado isoladamente. |
| `noUncheckedIndexedAccess` | `true` | Considera possíveis valores `undefined` ao acessar índices. |
| `noUnusedLocals` | `true` | Reporta variáveis e importações sem uso. |
| `noUnusedParameters` | `true` | Reporta parâmetros sem uso. |
| `noFallthroughCasesInSwitch` | `true` | Impede casos `switch` com fallthrough não intencional. |
| `noImplicitOverride` | `true` | Exige `override` ao sobrescrever métodos. |
| `types` | `["bun"]` | Inclui os tipos globais do Bun. |

## Desenvolvimento

Pré-requisitos:

- Git
- Bun

Instale as dependências e execute a verificação de tipos:

```bash
bun install
bun run check-types
```

Para validar os commits conventional em relação à `main`:

```bash
bun run lint:commit
```

O repositório usa [Conventional Commits](https://www.conventionalcommits.org/) e commitlint. Exemplos:

```text
fix(base): corrig moduleResolution
docs: atualizar instruções de uso
chore: atualizar dependências
```

Leia [CONTRIBUTING.md](./CONTRIBUTING.md) para mais detalhes sobre o fluxo de contribuição.

## Estrutura

- `tsconfig.base.json`: configuração distribuída para os projetos consumidores.
- `tsconfig.json`: configuração usada para verificar este próprio repositório.
- `package.json`: metadados, exportação e scripts do pacote.
- `release-please-config.json`: configuração dos releases automatizados.

## Licença

Este projeto é proprietário e está sob os termos descritos no [LICENSE](./LICENSE). Todos os direitos são reservados à AeroOps.
