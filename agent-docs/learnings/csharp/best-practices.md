# C# best practices

Updated: 2026-10-02. Read before writing or changing any C# code. Follow coherent project-specific rules; for Unity, also read its C# conventions.

- **Names:** PascalCase for types, methods, properties, events, constants, and enum members; `I` prefix for interfaces. camelCase for parameters, locals, and private fields. No underscore prefixes; use `this.fieldName` to resolve name collisions. Use clear domain terms, action verbs, boolean conditions, and units where needed.
- **Layout:** Four spaces; braces on separate lines, including single-statement branches and loops. One statement or field per line. One blank line between methods and logical steps; keep related statements together. Aim for lines under 120 characters.
- **Fields:** Constants, static fields, instance dependencies/configuration, then runtime state. Put readonly before mutable fields within a group. Group by responsibility, not alphabetically or by type; separate groups with one blank line. Preserve initializer dependencies.
- **Members:** One principal type per matching file. Fields, constructors, properties/events, public operations, then private helpers. Keep related members together. Avoid decorative regions.
- **Methods:** One understandable operation; use early returns to reduce nesting. Extract meaningful helpers, not tiny wrappers to satisfy a line limit. Prefer named intermediate values over dense chains or nested ternaries. Expression bodies suit simple members; use blocks for multi-step work.
- **Types:** Use `var` when the type is obvious; explicit types when they clarify a result. Use syntax and APIs supported by the project's compiler and runtime.
- **Design:** Encapsulate mutable state. Make inputs, results, ownership, and failure behavior clear. Use `Try...` for expected refusal and exceptions for broken contracts. Add interfaces or patterns only when they simplify a real boundary or variation.
- **Comments:** Explain only non-obvious reasons, constraints, or contracts. Do not narrate operations or document every member. Prefer clear names and structure.
- **Finish:** Format changed code, inspect the diff for these rules, and run relevant behavior checks. Keep broad style cleanup separate from behavior changes; preserve public APIs and Unity serialized data unless migration is part of the task.
