# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Gmail MCP (Model Context Protocol) server that provides Gmail integration for Claude Desktop with OAuth2 authentication support. It's a TypeScript Node.js application that serves as a bridge between AI assistants and Gmail API.

## Development Commands

### Build and Run
```bash
# Build TypeScript to JavaScript
npm run build

# Start the server with access token
npm start YOUR_ACCESS_TOKEN_HERE

# Or with environment variable
GMAIL_ACCESS_TOKEN=YOUR_TOKEN npm start
```

### Testing
```bash
# Run MCP evaluations
OPENAI_API_KEY=your-key npx mcp-eval src/evals/evals.ts src/index.ts
```

### Publishing
```bash
# Build is automatically run via prepare script
npm publish
```

## Architecture

### Core Components

1. **Main Server (`src/index.ts`)**
   - MCP server implementation using StdioServerTransport
   - OAuth2 authentication flow with browser launch
   - Gmail API integration for all email operations
   - Tool definitions and request handlers

2. **Label Manager (`src/label-manager.ts`)**
   - CRUD operations for Gmail labels
   - Label visibility configuration
   - Find or create label functionality

3. **Filter Manager (`src/filter-manager.ts`)**
   - Gmail filter creation and management
   - Pre-built filter templates for common scenarios
   - Filter criteria and action definitions

4. **Utilities (`src/utl.ts`)**
   - Email message creation with MIME encoding
   - Nodemailer integration for attachment support
   - Email validation and header encoding

### Authentication

The server uses direct access token authentication:
1. Accepts access token via command-line argument or environment variable
2. No OAuth flow or credential storage needed
3. Token must have Gmail API scopes: `gmail.modify` and `gmail.settings.basic`

### Key Features

- **Email Operations**: Send, draft, read, search, modify, delete emails
- **Attachment Support**: Full send/receive attachment capabilities using Nodemailer
- **Label Management**: Complete CRUD operations for Gmail labels
- **Filter Management**: Create and manage Gmail filters with templates
- **Batch Operations**: Efficient batch processing for multiple emails
- **International Support**: Handles non-ASCII characters properly

### Tool Registration

All Gmail operations are exposed as MCP tools with Zod schema validation:
- Email tools: send_email, draft_email, read_email, search_emails, etc.
- Label tools: create_label, update_label, delete_label, list_email_labels
- Filter tools: create_filter, list_filters, delete_filter, create_filter_from_template
- Batch tools: batch_modify_emails, batch_delete_emails

### Environment Variables

- `GMAIL_ACCESS_TOKEN`: Gmail API access token (alternative to command-line argument)