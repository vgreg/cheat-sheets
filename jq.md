# jq — Quick Cheat Sheet

*Practical reference for exploring and transforming JSON from the CLI.*

## Basics 🧩

* **General form:**

  ```bash
  jq '<filter>' file.json
  ```

* **Pretty-print JSON:**

  ```bash
  jq . file.json
  ```

* **Compact output:**

  ```bash
  jq -c . file.json
  ```

* **Read from stdin:**

  ```bash
  cat file.json | jq .
  ```

* **Save result:**

  ```bash
  jq '.key' file.json > out.json
  ```

---

## Navigation 🔍

* **Root:** `.`
* **Object key:** `.key`
* **Nested keys:** `.user.name`
* **Array index:** `.[0]`, `.[-1]` (last)
* **Slice array:** `.[0:5]` (0–4)
* **Multiple keys:** `.user, .id`
* **Wildcard keys:** `.[]` (iterate array or object values)

Example:

```bash
jq '.users[0].email' data.json
```

---

## Filtering Arrays 🎯

* **Iterate elements:** `.[]`
* **Filter by condition:** `.[] | select(.age > 30)`
* **Multiple conditions:** `.[] | select(.age > 30 and .active == true)`
* **Containment:** `.[] | select(.tags[] == "finance")`
* **Exists:** `.[] | select(has("email"))`

---

## Object & Field Selection 🧱

* **Single field:** `.field`
* **Subset of fields:** `{id, name}`
* **Rename fields:** `{identifier: .id, username: .name}`
* **Add new fields:** `. + {country: "CA"}`
* **Remove field:** `del(.unneeded_key)`
* **Flatten nested object:** `.user | {name, email}`

---

## Aggregation & Counting 📊

* **Count elements:** `. | length`
* **Count after filter:** `.[] | select(.active) | length`
* **Unique values:** `.[] | .category | unique`
* **Group by key:**

  ```bash
  jq 'group_by(.type) | map({type: .[0].type, count: length})' file.json
  ```

* **Sum numeric field:** `.[] | .amount | add`
* **Average:**

  ```bash
  jq '[.[] | .amount] | add / length' file.json
  ```

---

## Sorting & Ordering 🔄

* **Sort array:** `sort`
* **Sort by key:** `sort_by(.age)`
* **Reverse order:** `sort_by(.score) | reverse`

Example:

```bash
jq 'sort_by(.age) | .[:5]' users.json
```

---

## String Operations ✂️

* **Upper/lowercase:** `ascii_upcase`, `ascii_downcase`
* **Split & join:** `split(",")`, `join(";")`
* **Regex match:** `test("foo")`
* **Substitute:** `sub("foo"; "bar")` (first) / `gsub("foo"; "bar")` (global)

Example:

```bash
jq '.[] | .email |= ascii_downcase' users.json
```

---

## Combining Filters ⚙️

* **Pipe:** `.[] | .name`
* **Combine into array:** `[.[] | .id]`
* **Multiple transforms:** `.[] | {id, user: .name | ascii_upcase}`
* **Conditional logic:**

  ```bash
  jq 'if .age > 65 then "senior" else "adult" end' file.json
  ```

* **Default values:** `.foo // "N/A"`

---

## JSON Structure Tools 🧭

* **Keys only:** `keys`
* **Key-value pairs:** `to_entries`
* **Rebuild from pairs:** `from_entries`
* **Get types:** `.[] | type`
* **Flatten arrays:** `[.[][]]`

---

## Input & Output 🧰

* **Multiple files:**

  ```bash
  jq -s '[.[][]]' file1.json file2.json
  ```

  (`-s` = “slurp” → read as array of files)
* **Compact one-liner output:**

  ```bash
  jq -cr '.[] | [.id, .name] | @csv' file.json
  ```

* **Export CSV:**

  ```bash
  jq -r '.[] | [.id, .name, .age] | @csv' file.json > out.csv
  ```

---

## Environment & Shell Integration 🧩

* **Pass shell vars:**

  ```bash
  jq --arg name "$USER" '. + {user: $name}' file.json
  ```

* **Read from command:**

  ```bash
  jq --slurpfile cfg config.json '. + {cfg: $cfg[0]}' data.json
  ```

* **Filter JSON from API:**

  ```bash
  curl -s https://api.example.com | jq '.results[] | {id, title}'
  ```

---

## Debugging & Inspection 🐞

* **Show type:** `type`
* **Inspect path/value pairs:** `path(..) as $p | getpath($p)`
* **Print keys with values:** `to_entries[] | "\(.key)=\(.value)"`
* **Color off (for scripts):** `--monochrome-output`

---

## Common Patterns 💡

* **List unique top-level keys:**

  ```bash
  jq 'keys_unsorted' file.json
  ```

* **Find all null fields:**

  ```bash
  jq 'paths(. == null)' file.json
  ```

* **Pretty-print nested array:**

  ```bash
  jq '.[][]' file.json
  ```

* **Select by substring:**

  ```bash
  jq '.[] | select(.name | contains("Vincent"))' users.json
  ```

---

## Minimal Survival Kit

* View: `jq . file.json`
* Filter: `jq '.[] | {id, name}' file.json`
* Search: `jq '.[] | select(.status=="active")' file.json`
* Count: `jq '. | length' file.json`
* Export: `jq -r '.[] | [.id, .score] | @csv' file.json`
* Transform: `jq '.[] | .name |= ascii_upcase' file.json`

*Think of jq as “JSON grep + awk + sed + SQL” — all at once.*
