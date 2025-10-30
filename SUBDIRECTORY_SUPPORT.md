# Subdirectory Support for flatnotes

This implementation adds support for organizing notes in subdirectories using forward slash (`/`) syntax in note titles.

## 🚀 Feature Overview

Users can now create and organize notes in hierarchical directory structures directly from the flatnotes interface, enabling better organization and categorization of notes.

## 📁 How It Works

### Creating Subdirectory Notes

When creating a new note, simply use forward slashes (`/`) in the title to specify the desired directory structure:

```
Title: Linux/how-to-get-docker
```

This will:
- Automatically create the `Linux/` directory if it doesn't exist
- Store the note as `Linux/how-to-get-docker.md`
- Make the note accessible via API and web interface

### Directory Structure Examples

```
notes/
├── Linux/
│   ├── how-to-get-docker.md
│   ├── package-management.md
│   └── system-administration.md
├── Development/
│   ├── python/
│   │   ├── virtualenv-guide.md
│   │   └── django-setup.md
│   ├── javascript/
│   │   └── es6-features.md
│   └── web-development.md
├── Projects/
│   ├── project-alpha/
│   │   ├── requirements.md
│   │   └── architecture.md
│   └── project-beta/
│       └── timeline.md
└── standalone-note.md
```

## 🔧 Technical Implementation

### Frontend Changes

**File: `client/views/Note.vue`**
- Updated validation regex to allow forward slashes
- Changed from: `/[<>:"/\\|?*]/` 
- Changed to: `/[<>:"\\|?*]/`
- Updated error message to remove forward slash from prohibited characters

### Backend Changes

**File: `server/helpers.py`**
- Updated `is_valid_filename()` function to allow forward slashes
- Removed `/` from the invalid characters list
- Updated error message accordingly

**File: `server/notes/file_system/file_system.py`**
- Enhanced `_write_file()` method to create parent directories automatically
- Added `os.makedirs(os.path.dirname(filepath), exist_ok=True)` before file creation
- Fixed `_list_all_note_filenames()` to use recursive glob pattern
- Changed from `glob.glob("*.md")` to `glob.glob("**/*.md", recursive=True)`
- Updated to return relative paths using `os.path.relpath()`

**File: `server/main.py`**
- Updated API routes to use `{title:path}` parameter instead of `{title}`
- This allows FastAPI to capture the full path including slashes
- Applied to both API endpoints (`/api/notes/{title:path}`) and web interface (`/note/{title:path}`)

## 📱 User Interface

### Creating Notes
1. Click "New Note" in the flatnotes interface
2. Enter title with slashes: `Development/python/virtualenv-guide`
3. Add content and save
4. The note is automatically organized in the specified directory structure

### Accessing Notes
- **Web Interface**: Navigate to `http://your-flatnotes-url/note/Development/python/virtualenv-guide`
- **API**: `GET /api/notes/Development/python/virtualenv-guide`
- **Search**: Subdirectory notes appear in search results with full paths

### Search Functionality
- Full-text search works across all notes regardless of directory depth
- Search results show complete note titles including directory paths
- Title highlighting works on the full path (e.g., `Development/<highlight>python</highlight>/virtualenv-guide`)

## ✅ Features Supported

- ✅ **Multi-level subdirectories** (e.g., `Projects/alpha/documentation`)
- ✅ **Automatic directory creation** (no manual setup required)
- ✅ **Full search integration** (subdirectory notes are searchable)
- ✅ **Web interface navigation** (direct URLs to subdirectory notes)
- ✅ **API compatibility** (all endpoints work with subdirectory paths)
- ✅ **Backward compatibility** (existing notes continue to work)
- ✅ **Content highlighting** (search works on subdirectory note content)

## 🔄 Migration Notes

### For Existing Users
- No migration required - existing notes continue to work unchanged
- New subdirectory structure can be created alongside existing notes
- Mixed organization (root notes + subdirectory notes) is fully supported

### File System Impact
- Notes are stored as normal markdown files in directory structure
- No database changes - uses existing file-based storage
- Search index automatically updated to include new file locations

## 🚦 Usage Examples

### Technical Documentation
```
Linux/networking/tcp-ip-basics.md
Linux/security/firewall-configuration.md
Development/databases/postgresql-setup.md
Development/databases/mysql-optimization.md
```

### Project Management
```
Projects/website-rewrite/requirements.md
Projects/website-rewrite/implementation-plan.md
Projects/mobile-app/api-design.md
Projects/mobile-app/user-stories.md
```

### Personal Organization
```
Personal/recipes/pasta-dishes.md
Personal/travel/europe-2024.md
Personal/finance/investment-strategy.md
Personal/health/workout-routine.md
```

## 🛠️ Development Notes

### Docker Compose Configuration
- `docker-compose.yaml`: Production build from GitHub repository
- `docker-compose.fork.yaml`: Local development build from source
- Both configurations support the new subdirectory functionality

### Testing
The implementation has been tested with:
- Single-level subdirectories: `Linux/docker-setup`
- Multi-level subdirectories: `Development/python/virtualenv-guide`
- Special characters in names: `Projects/project-alpha/requirements-v2.0`
- Search functionality across all directory levels
- Web interface navigation to subdirectory notes

## 📝 Limitations

- **Forward slash is the only directory separator** - backslashes are still filtered for filename safety
- **No empty directory segments** - titles like `Linux//empty` are not allowed
- **Maximum path length** - subject to operating system file path limitations
- **Character restrictions** - other invalid characters (`<>:"\\|?*`) are still enforced

## 🎯 Benefits

1. **Better Organization**: Group related notes together in logical directories
2. **Improved Navigation**: Direct access to specific categories of notes
3. **Enhanced Search**: Search within specific topics or across all notes
4. **Scalability**: No limit on directory depth or number of subdirectories
5. **Flexibility**: Mix flat and hierarchical organization as needed

---

**Implementation Status**: ✅ Complete and Tested  
**Compatibility**: ✅ Backward Compatible  
**Deployment**: ✅ Ready for Production