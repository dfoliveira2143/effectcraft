# EffectCraft — funcionalidades e módulos

Última atualização: 2026-10-28.

## Fluxo principal

| Etapa | Entradas | Responsabilidade e dependências | Saídas |
|---|---|---|---|
| Abrir e editar | Footage, composições, propriedades e keyframes | [project](../../crates/project/), [media](../../crates/media/), [keyframe](../../crates/keyframe/), [engine](../../crates/engine/) | Composições avaliadas no tempo e fila de renderização |
| Avaliar e produzir saída | Estado editado e parâmetros de saída | [effects](../../crates/effects/), [expr](../../crates/expr/), [render](../../crates/render/), [gpu](../../crates/gpu/), [export](../../crates/export/) | Frames, sequências de imagens e mídia codificada |
| Acesso pelo usuário ou agente | Gestos, argumentos CLI ou chamadas estruturadas | [apps](../../apps/), [engine](../../crates/engine/) e interfaces descritas abaixo | Estado atualizado, resposta ou erro; comportamento detalhado nos contratos |

A tabela mostra o caminho conceitual, não uma garantia de suporte a todos os formatos. APIs, estruturas, unidades, efeitos colaterais e limites ficam na [arquitetura](../architecture.md), nos documentos de assunto e nos módulos indicados.

## Contratos e exemplos de uso

- [effects.md](../effects.md)
- [footage.md](../footage.md)
- [scripting-parity.md](../scripting-parity.md)
- [plugins.md](../plugins.md)
- [testing.md](../testing.md)
- [control-protocol.md](../control-protocol.md)
- [mcp.md](../mcp.md)

## Inventário de bibliotecas

Responsabilidades resumidas a partir dos manifestos e comentários de módulo do checkout. As dependências listadas são as internas diretas declaradas em `dependencies` (incluindo condicionais); não incluem dependências de teste/build nem expansão de features. O manifesto de cada linha é a fonte exata.

| Módulo / manifesto | Responsabilidade declarada | Dependências internas diretas |
|---|---|---|
| [automation](../../crates/automation/Cargo.toml) | EffectCraft MCP server (JSON-RPC 2.0 over stdio) over a headless session or bridged to the running app's control channel | `effectcraft-engine` |
| [av1enc](../../crates/av1enc/Cargo.toml) | Clean-room pure-Rust AV1 encoder (Main profile, key and inter frames) for MP4/WebM export, from the AV1 Bitstream & Decoding Process Specification | Nenhuma declarada nesta categoria |
| [color](../../crates/color/Cargo.toml) | Colour for EffectCraft: RGBA values, sRGB transfer, HSL/HSV, luminance, the label palette and the 38 After-Effects-compatible blend modes. | Nenhuma declarada nesta categoria |
| [effects](../../crates/effects/Cargo.toml) | Effect registry (specs, parameters, categories) and CPU implementations of the effects library | `effectcraft-color`, `effectcraft-geom`, `effectcraft-keyframe`, `effectcraft-project`, `effectcraft-raster`, `effectcraft-segment`, `effectcraft-track` |
| [engine](../../crates/engine/Cargo.toml) | The EffectCraft engine: session, undo history, editor state and the command registry every frontend (UI, CLI, control channel, MCP) dispatches through | `effectcraft-color`, `effectcraft-effects`, `effectcraft-geom`, `effectcraft-interchange`, `effectcraft-keyframe`, `effectcraft-lottie`, `effectcraft-model`, `effectcraft-path`, `effectcraft-pdf`, `effectcraft-project`, `effectcraft-psd`, `effectcraft-raster`, `effectcraft-render`, `effectcraft-segment`, `effectcraft-svg`, `effectcraft-text`, `effectcraft-time`, `effectcraft-track` |
| [export](../../crates/export/Cargo.toml) | Render Queue jobs and output modules: H.264 MP4 (+AAC), ProRes and uncompressed MOV (+PCM) via FilmCraft, HEVC / AV1 MP4 (+AAC) and WebM (VP9 or AV1 + Opus) via EffectCraft encoders, PNG/JPEG/TIFF/EXR sequences, animated GIF, WAV/AIFF | `effectcraft-av1enc`, `effectcraft-hevcenc`, `effectcraft-opusenc`, `effectcraft-project`, `effectcraft-raster`, `effectcraft-render`, `effectcraft-time`, `effectcraft-vp9enc` |
| [expr](../../crates/expr/Cargo.toml) | After Effects-compatible expressions: a boa JavaScript runtime with the AE object model (thisComp, thisLayer, wiggle, loopOut, valueAtTime…) and array maths | `effectcraft-geom`, `effectcraft-keyframe`, `effectcraft-path`, `effectcraft-project`, `effectcraft-render`, `effectcraft-time` |
| [geom](../../crates/geom/Cargo.toml) | Geometry for EffectCraft: 2D/3D vectors, 3×3 (2D projective) and 4×4 matrices, rectangles and the layer-transform composition used by the compositor. | Nenhuma declarada nesta categoria |
| [gpu](../../crates/gpu/Cargo.toml) | The GPU compositor (wgpu compute): layer transforms, masks/mattes, the 38 blend modes, motion blur, bit-depth quantisation and GPU effects, with the CPU renderer as the reference | `effectcraft-color`, `effectcraft-effects`, `effectcraft-geom`, `effectcraft-keyframe`, `effectcraft-project`, `effectcraft-raster`, `effectcraft-render`, `effectcraft-time` |
| [hevcenc](../../crates/hevcenc/Cargo.toml) | Clean-room pure-Rust HEVC (H.265) encoder (Main / Main 10, I and P slices, CABAC) for MP4 export, from ITU-T H.265 | Nenhuma declarada nesta categoria |
| [host](../../crates/host/Cargo.toml) | Builds a fully wired EffectCraft session: media decoding (FilmCraft codecs), importer, the expression engine and scripting | `effectcraft-engine`, `effectcraft-export`, `effectcraft-expr`, `effectcraft-media`, `effectcraft-plugin`, `effectcraft-project`, `effectcraft-script` |
| [interchange](../../crates/interchange/Cargo.toml) | Premiere Pro interop through timeline interchange: Final Cut Pro 7 XML (xmeml), FCPXML, OpenTimelineIO and CMX 3600 EDL sequences to and from compositions (via filmcraft-interchange) | `effectcraft-color`, `effectcraft-keyframe`, `effectcraft-project`, `effectcraft-time` |
| [keyframe](../../crates/keyframe/Cargo.toml) | Animated values: keyframes, temporal ease (speed/influence), spatial Bezier motion paths, hold/linear/auto interpolation | `effectcraft-color`, `effectcraft-geom`, `effectcraft-time` |
| [lottie](../../crates/lottie/Cargo.toml) | Lottie JSON import and export: compositions, precomps, shape/solid/null/image/text layers, keyframes with temporal and spatial easing, masks, track mattes and a few effects | `effectcraft-color`, `effectcraft-effects`, `effectcraft-keyframe`, `effectcraft-path`, `effectcraft-project`, `effectcraft-text`, `effectcraft-time` |
| [media](../../crates/media/Cargo.toml) | Footage import: probing, stills, image sequences, video and audio via FilmCraft's pure-Rust codecs, memory-budgeted frame cache | `effectcraft-model`, `effectcraft-pdf`, `effectcraft-project`, `effectcraft-psd`, `effectcraft-raster`, `effectcraft-render`, `effectcraft-svg`, `effectcraft-time` |
| [model](../../crates/model/Cargo.toml) | 3D models: glTF 2.0 (.gltf/.glb), Wavefront OBJ/MTL, STL and FBX 7.x (binary and ASCII) import, PBR metallic-roughness materials, node hierarchy, skinning and animation, parametric primitives, and extruded/bevelled outline meshes | `effectcraft-geom` |
| [opusenc](../../crates/opusenc/Cargo.toml) | Clean-room pure-Rust Opus encoder (RFC 6716 SILK NB/MB/WB, hybrid SWB/FB and CELT FB modes, 48 kHz, 20 ms; RFC 7845 OpusHead) for WebM export audio | Nenhuma declarada nesta categoria |
| [path](../../crates/path/Cargo.toml) | Bezier shapes: AE vertex model ⇄ kurbo, parametric shapes, path operations, stroking/dashing and anti-aliased coverage rasterisation | `effectcraft-geom`, `effectcraft-keyframe`, `effectcraft-raster` |
| [pdf](../../crates/pdf/Cargo.toml) | Vector footage import: PDF and PDF-compatible Illustrator (.ai) pages and EPS (a PostScript subset) parsed into the SVG render tree (paths, fills, strokes, shadings, tiling patterns, clipping, text with embedded fonts, images, blend modes, soft masks, optional-content layers) | `effectcraft-svg`, `effectcraft-text` |
| [plugin](../../crates/plugin/Cargo.toml) | EffectCraft effect plug-ins: loads sandboxed WebAssembly effect modules (plug-in API v1) into the effect registry | `effectcraft-effects` |
| [project](../../crates/project/Cargo.toml) | The EffectCraft document: project items, compositions, layers and the After-Effects-style property tree | `effectcraft-color`, `effectcraft-geom`, `effectcraft-keyframe`, `effectcraft-time` |
| [psd](../../crates/psd/Cargo.toml) | Photoshop PSD/PSB reader (layers, groups, masks, blend modes, text, layer effects, adjustment layers; 8/16/32-bit, RGB/CMYK/Gray/Lab/Indexed, raw/RLE/ZIP) and a minimal writer, from Adobe's published file format specification | Nenhuma declarada nesta categoria |
| [raster](../../crates/raster/Cargo.toml) | f32 premultiplied RGBA images: sampling, projective warps, blurs, convolution, compositing (rayon-parallel) | `effectcraft-color`, `effectcraft-geom` |
| [render](../../crates/render/Cargo.toml) | The CPU compositor: property evaluation, layer sources (solids, footage, text, shapes, precomps), masks, effects, track mattes, 2D/3D transforms, motion blur and blending | `effectcraft-color`, `effectcraft-effects`, `effectcraft-geom`, `effectcraft-keyframe`, `effectcraft-model`, `effectcraft-path`, `effectcraft-project`, `effectcraft-raster`, `effectcraft-text`, `effectcraft-time` |
| [script](../../crates/script/Cargo.toml) | EffectCraft scripting: JavaScript (boa) with an After Effects-style object model (app, project, CompItem, Layer, Property…) over the engine's undoable commands | `effectcraft-engine`, `effectcraft-keyframe`, `effectcraft-path`, `effectcraft-project`, `effectcraft-render`, `effectcraft-time` |
| [segment](../../crates/segment/Cargo.toml) | Swappable trained models for Roto Brush and face tracking: the MaskModel and FaceModel interfaces, a registry of open-licensed models, PyTorch checkpoint and TensorFlow Lite readers, a pure-Rust MobileSAM and MediaPipe Face Landmarker | Nenhuma declarada nesta categoria |
| [svg](../../crates/svg/Cargo.toml) | SVG import: parses SVG 1.1 / SVG 2 static vector content (paths, basic shapes, groups, transforms, use, CSS styles, solid and gradient fills/strokes, opacity) and rasterises it at any scale | `effectcraft-geom`, `effectcraft-path`, `effectcraft-raster` |
| [text](../../crates/text/Cargo.toml) | Text engine: fonts (bundled OFL + system), shaping (harfrust), bidi, line breaking, paragraph layout and glyph outlines as Bezier paths | `effectcraft-keyframe` |
| [time](../../crates/time/Cargo.toml) | Exact media time for EffectCraft (shared design with FilmCraft). | Nenhuma declarada nesta categoria |
| [track](../../crates/track/Cargo.toml) | Motion tracking: feature/search region point tracking (pyramid normalized cross-correlation + Lucas-Kanade refinement), confidence, corner-pin / transform solves, stabilization, Roto Brush segmentation (graph cut, matting) and 3D camera solving (structure from motion) | `effectcraft-raster`, `effectcraft-segment` |
| [ui-egui](../../crates/ui-egui/Cargo.toml) | The EffectCraft egui frontend (swappable): docking panels, composition viewer, timeline, effect controls, control channel | `effectcraft-color`, `effectcraft-engine`, `effectcraft-geom`, `effectcraft-gpu`, `effectcraft-keyframe`, `effectcraft-path`, `effectcraft-project`, `effectcraft-render`, `effectcraft-text`, `effectcraft-time` |
| [vp9enc](../../crates/vp9enc/Cargo.toml) | VP9 encoder (profile 0, 8-bit 4:2:0; intra + inter frames, loop filter, rate control) for WebM export, from the VP9 Bitstream & Decoding Process Specification | Nenhuma declarada nesta categoria |

Ao mudar uma entrada, saída ou dependência, atualize primeiro o contrato do módulo e depois esta navegação. [Voltar ao índice](../INDEX.md).
