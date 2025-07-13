import json
import os
import time
from pathlib import Path
import requests
from karakeep_python_api import KarakeepAPI

# === Prompt for configuration ===
API_ENDPOINT = input("🔐 Enter Karakeep API endpoint (e.g. https://karakeep.example.com/api/v1): ").strip()
API_KEY = input("🔑 Enter Karakeep API key: ").strip()
SRC_DIR_INPUT = input("📁 Enter full path to your bookmarks folder: ").strip()

os.environ["KARAKEEP_PYTHON_API_ENDPOINT"] = API_ENDPOINT
os.environ["KARAKEEP_PYTHON_API_KEY"] = API_KEY
SRC_DIR = Path(SRC_DIR_INPUT).expanduser().resolve()

HEADERS = {
    "Authorization": f"Bearer {API_KEY}",
    "Content-Type": "application/json"
}

# === Initialize API client ===
api = KarakeepAPI()
count = errors = 0

# === Create tag if it doesn't exist ===
def create_tag_if_not_exists(name):
    try:
        api.create_a_new_tag(name)
        print(f"✅ Created tag: {name}")
        time.sleep(0.3)
    except Exception as e:
        if "already exists" in str(e):
            print(f"⚠️ Tag '{name}' already exists, skipping")
        else:
            raise

# === Get current tag name -> ID mapping ===
def get_tag_map():
    res = api.get_all_tags()
    tags_list = res.get("tags") if isinstance(res, dict) else list(res)
    return {
        (t["name"] if isinstance(t, dict) else t.name):
        (t["id"] if isinstance(t, dict) else t.id)
        for t in tags_list
    }

# === Phase 1: Pre-create all tags ===
for folder in SRC_DIR.iterdir():
    info = folder / "info.json"
    if not info.exists():
        continue
    try:
        data = json.loads(info.read_text())
        for tag in data.get("Labels") or []:
            create_tag_if_not_exists(tag)
    except Exception as e:
        errors += 1
        print(f"❌ Error pre-creating tags in '{folder.name}': {e}")

tag_map = get_tag_map()

# === Phase 2: Import bookmarks and assign tags ===
for folder in SRC_DIR.iterdir():
    info = folder / "info.json"
    if not info.exists():
        continue
    try:
        data = json.loads(info.read_text())
        url = data.get("URL")
        if not url:
            print(f"⚠️ Skipping '{folder.name}': no URL")
            continue

        tags = data.get("Labels") or []
        tag_ids = [tag_map[t] for t in tags if t in tag_map]

        bm = api.create_a_new_bookmark(
            type="link",
            url=url,
            title=data.get("Title") or None,
            createdAt=data.get("Created"),
            archived=data.get("IsArchived", False)
        )
        bid = bm.id
        print(f"✅ Created bookmark: '{bm.title or url}'")
        print(f"Tag IDs for bookmark '{bm.title or url}': {tag_ids}")

        if tag_ids:
            tag_payload = {"tags": [{"tagId": tid} for tid in tag_ids]}
            print(f"Tag payload for bookmark '{bm.title or url}': {tag_payload}")

            response = requests.post(
                f"{API_ENDPOINT}/bookmarks/{bid}/tags",
                headers=HEADERS,
                json=tag_payload
            )

            if response.ok:
                print("🔖 Tags assigned successfully.")
            else:
                print(f"❌ Tag assignment error: {response.status_code} {response.text}")
                errors += 1
        else:
            print(f"⚠️ No valid tags to assign for: '{bm.title or url}'")

        count += 1
    except Exception as e:
        errors += 1
        print(f"❌ Error importing '{folder.name}': {e}")

# === Final report ===
print(f"\n✅ Done — {count} bookmarks imported, {errors} errors")
