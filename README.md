# peasy-image

[![crates.io](https://img.shields.io/crates/v/peasy-image)](https://crates.io/crates/peasy-image)
[![docs.rs](https://docs.rs/peasy-image/badge.svg)](https://docs.rs/peasy-image)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
[![crates.io](https://agentgif.com/badge/crates/peasy-image/version.svg)](https://crates.io/crates/peasy-image)
[![GitHub stars](https://agentgif.com/badge/github/peasytools/peasy-image-rs/stars.svg)](https://github.com/peasytools/peasy-image-rs)

Async Rust client for the [PeasyImage](https://peasyimage.com) API — compress, resize, convert, and crop images. Built with reqwest, serde, and tokio.

Built from [PeasyImage](https://peasyimage.com), a comprehensive image processing toolkit offering free online tools for compressing, resizing, converting, and cropping images across all major formats. The site includes in-depth guides on image optimization for the web, format comparisons between WebP, AVIF, PNG, and JPEG, plus a glossary covering concepts from color spaces to alpha channels to EXIF metadata.

> **Try the interactive tools at [peasyimage.com](https://peasyimage.com)** — [Compress Image](https://peasyimage.com/image/compress-image/), [Resize Image](https://peasyimage.com/image/resize-image/), [Convert Image](https://peasyimage.com/image/convert-image/), [Crop Image](https://peasyimage.com/image/crop-image/), and more.

<p align="center">
  <a href="https://agentgif.com/S4rXdfoO"><img src="https://media.agentgif.com/S4rXdfoO.gif" alt="peasy-image demo — image compress, resize, and convert tools in Rust terminal" width="800"></a>
</p>

## Table of Contents

- [Install](#install)
- [Quick Start](#quick-start)
- [What You Can Do](#what-you-can-do)
  - [Image Processing Operations](#image-processing-operations)
  - [Browse Reference Content](#browse-reference-content)
  - [Search and Discovery](#search-and-discovery)
- [API Client](#api-client)
  - [Available Methods](#available-methods)
- [Learn More About Image Tools](#learn-more-about-image-tools)
- [Also Available](#also-available)
- [Peasy Developer Tools](#peasy-developer-tools)
- [License](#license)

## Install

```toml
[dependencies]
peasy-image = "0.2.0"
tokio = { version = "1", features = ["full"] }
```

Or via cargo:

```bash
cargo add peasy-image
```

## Quick Start

```rust
use peasy_image::Client;

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let client = Client::new();

    // List available image tools
    let tools = client.list_tools(&Default::default()).await?;
    for tool in &tools.results {
        println!("{}: {}", tool.name, tool.description);
    }

    Ok(())
}
```

## What You Can Do

### Image Processing Operations

Digital images are stored in dozens of formats, each optimized for different use cases. JPEG uses lossy compression ideal for photographs, PNG supports lossless compression with alpha channel transparency, WebP (developed by Google) offers both lossy and lossless modes at 25-34% smaller file sizes than JPEG, and AVIF (based on the AV1 video codec) pushes compression efficiency even further. PeasyImage provides tools to compress, resize, convert, and crop images across all these formats.

| Operation | Slug | Description |
|-----------|------|-------------|
| Compress Image | `compress-image` | Reduce file size while preserving visual quality |
| Resize Image | `resize-image` | Scale dimensions with aspect ratio preservation |
| Convert Image | `convert-image` | Transform between PNG, JPEG, WebP, AVIF, and more |
| Crop Image | `crop-image` | Extract rectangular regions from images |

```rust
use peasy_image::Client;

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let client = Client::new();

    // Retrieve the image compression tool and inspect its capabilities
    let tool = client.get_tool("compress-image").await?;
    println!("Tool: {}", tool.name);              // Image compression tool name
    println!("Description: {}", tool.description); // How compression works

    // List all available image tools with pagination
    let opts = peasy_image::ListOptions {
        page: Some(1),
        limit: Some(20),
        ..Default::default()
    };
    let tools = client.list_tools(&opts).await?;
    println!("Total image tools available: {}", tools.count);

    Ok(())
}
```

Learn more: [Compress Image Tool](https://peasyimage.com/image/compress-image/) · [How to Compress Images for Web](https://peasyimage.com/guides/how-to-compress-images-for-web/) · [Image Format Comparison](https://peasyimage.com/guides/image-format-comparison/)

### Browse Reference Content

PeasyImage includes a detailed glossary of image processing and digital media terminology, plus educational guides on format selection and optimization workflows. The glossary covers concepts like WebP (Google's modern image format with superior compression), EXIF (Exchangeable Image File Format metadata embedded by cameras), lossy vs. lossless compression trade-offs, color spaces (sRGB, Adobe RGB, Display P3), and alpha channels for transparency support.

| Term | Description |
|------|-------------|
| [WebP](https://peasyimage.com/glossary/webp/) | Google's modern image format — lossy and lossless modes |
| [EXIF](https://peasyimage.com/glossary/exif/) | Exchangeable Image File Format — camera metadata standard |
| [Lossy Compression](https://peasyimage.com/glossary/lossy-compression/) | Compression that discards data to achieve smaller files |
| [Color Space](https://peasyimage.com/glossary/color-space/) | Mathematical model defining the range of representable colors |
| [Alpha Channel](https://peasyimage.com/glossary/alpha-channel/) | Transparency layer in PNG, WebP, and AVIF images |

```rust
use peasy_image::Client;

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let client = Client::new();

    // Browse the image glossary for digital media terminology
    let glossary = client.list_glossary(&peasy_image::ListOptions {
        search: Some("webp".into()), // Search for modern image format concepts
        ..Default::default()
    }).await?;
    for term in &glossary.results {
        println!("{}: {}", term.term, term.definition);
    }

    // Read a guide on choosing the right image format
    let guide = client.get_guide("image-format-comparison").await?;
    println!("Guide: {} (Level: {})", guide.title, guide.audience_level);

    Ok(())
}
```

Learn more: [Image Glossary](https://peasyimage.com/glossary/) · [Image Format Comparison](https://peasyimage.com/guides/image-format-comparison/) · [How to Compress Images for Web](https://peasyimage.com/guides/how-to-compress-images-for-web/)

### Search and Discovery

The API supports full-text search across all content types — tools, glossary terms, guides, use cases, and format documentation. Search results are grouped by content type, making it easy to find the right tool or reference for any image processing workflow.

```rust
use peasy_image::Client;

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let client = Client::new();

    // Search across all image content — tools, glossary, guides, and formats
    let results = client.search("convert webp", Some(20)).await?;
    println!("Found {} tools, {} glossary terms, {} guides",
        results.results.tools.len(),
        results.results.glossary.len(),
        results.results.guides.len(),
    );

    // Discover format conversion paths — what can PNG convert to?
    let conversions = client.list_conversions(&peasy_image::ListConversionsOptions {
        source: Some("png".into()), // Find all formats PNG can be converted to
        ..Default::default()
    }).await?;
    for c in &conversions.results {
        println!("{} -> {}", c.source_format, c.target_format);
    }

    Ok(())
}
```

Learn more: [REST API Docs](https://peasyimage.com/developers/) · [All Image Tools](https://peasyimage.com/)

## API Client

The client wraps the [PeasyImage REST API](https://peasyimage.com/developers/) with strongly-typed Rust structs using serde deserialization.

```rust
use peasy_image::Client;

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    let client = Client::new();
    // Or with a custom base URL:
    // let client = Client::with_base_url("https://custom.example.com");

    // List tools with filters
    let opts = peasy_image::ListOptions {
        page: Some(1),
        limit: Some(10),
        search: Some("resize".into()),
        ..Default::default()
    };
    let tools = client.list_tools(&opts).await?;

    // Get a specific tool
    let tool = client.get_tool("image-resize").await?;
    println!("{}: {}", tool.name, tool.description);

    // Search across all content
    let results = client.search("resize", Some(20)).await?;
    println!("Found {} tools", results.results.tools.len());

    // Browse the glossary
    let glossary = client.list_glossary(&peasy_image::ListOptions {
        search: Some("jpeg".into()),
        ..Default::default()
    }).await?;
    for term in &glossary.results {
        println!("{}: {}", term.term, term.definition);
    }

    // Discover guides
    let guides = client.list_guides(&peasy_image::ListGuidesOptions {
        category: Some("images".into()),
        ..Default::default()
    }).await?;
    for guide in &guides.results {
        println!("{} ({})", guide.title, guide.audience_level);
    }

    // List format conversions
    let conversions = client.list_conversions(&peasy_image::ListConversionsOptions {
        source: Some("png".into()),
        ..Default::default()
    }).await?;

    Ok(())
}
```

### Available Methods

| Method | Description |
|--------|-------------|
| `list_tools(&opts)` | List tools (paginated, filterable) |
| `get_tool(slug)` | Get tool by slug |
| `list_categories(&opts)` | List tool categories |
| `list_formats(&opts)` | List file formats |
| `get_format(slug)` | Get format by slug |
| `list_conversions(&opts)` | List format conversions |
| `list_glossary(&opts)` | List glossary terms |
| `get_glossary_term(slug)` | Get glossary term |
| `list_guides(&opts)` | List guides |
| `get_guide(slug)` | Get guide by slug |
| `list_use_cases(&opts)` | List use cases |
| `search(query, limit)` | Search across all content |
| `list_sites()` | List Peasy sites |
| `openapi_spec()` | Get OpenAPI specification |

Full API documentation at [peasyimage.com/developers/](https://peasyimage.com/developers/).
OpenAPI 3.1.0 spec: [peasyimage.com/api/openapi.json](https://peasyimage.com/api/openapi.json).

## Learn More About Image Tools

- **Tools**: [Compress Image](https://peasyimage.com/image/compress-image/) · [Resize Image](https://peasyimage.com/image/resize-image/) · [Convert Image](https://peasyimage.com/image/convert-image/) · [Crop Image](https://peasyimage.com/image/crop-image/) · [All Tools](https://peasyimage.com/)
- **Guides**: [Image Format Comparison](https://peasyimage.com/guides/image-format-comparison/) · [How to Compress Images for Web](https://peasyimage.com/guides/how-to-compress-images-for-web/) · [All Guides](https://peasyimage.com/guides/)
- **Glossary**: [WebP](https://peasyimage.com/glossary/webp/) · [EXIF](https://peasyimage.com/glossary/exif/) · [Lossy Compression](https://peasyimage.com/glossary/lossy-compression/) · [Color Space](https://peasyimage.com/glossary/color-space/) · [Alpha Channel](https://peasyimage.com/glossary/alpha-channel/) · [All Terms](https://peasyimage.com/glossary/)
- **Formats**: [All Formats](https://peasyimage.com/formats/)
- **API**: [REST API Docs](https://peasyimage.com/developers/) · [OpenAPI Spec](https://peasyimage.com/api/openapi.json)

## Also Available

| Language | Package | Install |
|----------|---------|---------|
| **Python** | [peasy-image](https://pypi.org/project/peasy-image/) | `pip install "peasy-image[all]"` |
| **TypeScript** | [peasy-image](https://www.npmjs.com/package/peasy-image) | `npm install peasy-image` |
| **Go** | [peasy-image-go](https://pkg.go.dev/github.com/peasytools/peasy-image-go) | `go get github.com/peasytools/peasy-image-go` |
| **Ruby** | [peasy-image](https://rubygems.org/gems/peasy-image) | `gem install peasy-image` |

## Peasy Developer Tools

Part of the [Peasy Tools](https://peasytools.com) open-source developer ecosystem.

| Package | PyPI | npm | crates.io | Description |
|---------|------|-----|-----------|-------------|
| peasy-pdf | [PyPI](https://pypi.org/project/peasy-pdf/) | [npm](https://www.npmjs.com/package/peasy-pdf) | [crate](https://crates.io/crates/peasy-pdf) | PDF merge, split, rotate, compress — [peasypdf.com](https://peasypdf.com) |
| **peasy-image** | [PyPI](https://pypi.org/project/peasy-image/) | [npm](https://www.npmjs.com/package/peasy-image) | [crate](https://crates.io/crates/peasy-image) | Image resize, crop, convert, compress — [peasyimage.com](https://peasyimage.com) |
| peasy-audio | [PyPI](https://pypi.org/project/peasy-audio/) | [npm](https://www.npmjs.com/package/peasy-audio) | [crate](https://crates.io/crates/peasy-audio) | Audio trim, merge, convert, normalize — [peasyaudio.com](https://peasyaudio.com) |
| peasy-video | [PyPI](https://pypi.org/project/peasy-video/) | [npm](https://www.npmjs.com/package/peasy-video) | [crate](https://crates.io/crates/peasy-video) | Video trim, resize, thumbnails, GIF — [peasyvideo.com](https://peasyvideo.com) |
| peasy-css | [PyPI](https://pypi.org/project/peasy-css/) | [npm](https://www.npmjs.com/package/peasy-css) | [crate](https://crates.io/crates/peasy-css) | CSS minify, format, analyze — [peasycss.com](https://peasycss.com) |
| peasy-compress | [PyPI](https://pypi.org/project/peasy-compress/) | [npm](https://www.npmjs.com/package/peasy-compress) | [crate](https://crates.io/crates/peasy-compress) | ZIP, TAR, gzip compression — [peasytools.com](https://peasytools.com) |
| peasy-document | [PyPI](https://pypi.org/project/peasy-document/) | [npm](https://www.npmjs.com/package/peasy-document) | [crate](https://crates.io/crates/peasy-document) | Markdown, HTML, CSV, JSON conversion — [peasyformats.com](https://peasyformats.com) |
| peasytext | [PyPI](https://pypi.org/project/peasytext/) | [npm](https://www.npmjs.com/package/peasytext) | [crate](https://crates.io/crates/peasytext) | Text case conversion, slugify, word count — [peasytext.com](https://peasytext.com) |

## License

MIT
