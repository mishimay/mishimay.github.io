

```swift
    func fetchRecord(id: CKRecord.ID) async throws -> CKRecord? {
        do {
            return try await database.record(for: id)
        } catch {
            if (error as? CKError)?.code == .unknownItem {
                return nil
            } else {
                throw error
            }
        }
    }

    let record = try await fetchRecord(id: CKRecord.ID(recordName: "xxxxx"))
    if record == nil {
        // record not found
    }
```

