# peasy-image

[![crates.io](https://img.shields.io/crates/v/peasy-image)](https://crates.io/crates/peasy-image)
[![docs.rs](https://docs.rs/peasy-image/badge.svg)](https://docs.rs/peasy-image)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)

Async Rust client for the [PeasyImage](https://peasyimage.com) API — resize, crop, compress, convert, and watermark images. Built with reqwest, serde, and tokio.

Built from [PeasyImage](https://peasyimage.com), a free online image toolkit with tools for resizing, cropping, compressing, converting, and watermarking images across all major formats.

> **Try the interactive tools at [peasyimage.com](https://peasyimage.com)** — [Image Tools](https://peasyimage.com/), [Image Glossary](https://peasyimage.com/glossary/), [Image Guides](https://peasyimage.com/guides/)

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

## Learn More

- **Tools**: [Image Compress](https://peasyimage.com/image/compress-image/) · [Image Resize](https://peasyimage.com/image/resize-image/) · [Image Convert](https://peasyimage.com/image/convert-image/) · [All Tools](https://peasyimage.com/)
- **Guides**: [Image Format Comparison](https://peasyimage.com/guides/image-format-comparison/) · [How to Compress Images for Web](https://peasyimage.com/guides/how-to-compress-images-for-web/) · [All Guides](https://peasyimage.com/guides/)
- **Glossary**: [AVIF](https://peasyimage.com/glossary/avif/) · [Color Grading](https://peasyimage.com/glossary/color-grading-image/) · [Alpha Channel](https://peasyimage.com/glossary/alpha-channel/) · [All Terms](https://peasyimage.com/glossary/)
- **Formats**: [PNG](https://peasyimage.com/formats/png/) · [WebP](https://peasyimage.com/formats/webp/) · [All Formats](https://peasyimage.com/formats/)
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
