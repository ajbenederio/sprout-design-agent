# CLAUDE.md

@CONTRIBUTING.md
@CHANGELOG.md
@CODE_OF_CONDUCT.md
@README.md

## Design System
This project uses Toge v2 (shadcn-vue registry).

- **Prototype skill:** always use `toge:prototype` — never `prototype-agent` (that targets Toge v1 / spr-* components and will generate the wrong API)
- **Icons:** lucide-vue-next — do not use Phosphor or @iconify/vue
- **Never call** `mcp__design-system-toge__*` or `get_component` — MCP reflects Toge v1 and returns wrong data
- **Component imports:** always from `@/components/ui/toge-*` with the `Toge` prefix — never bare shadcn names
- **No v1:** `design-system-next`, `SproutDesignSystem`, and `spr-*` tags must not appear anywhere in this project
