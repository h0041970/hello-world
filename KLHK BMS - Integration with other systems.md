# KLHK BMS - Integration with other systems

### Retriving jobs from eForm

- Called in a periodic manner (e.g. every 15 minutes)
- Retrieving latest JR / RR / IWA (by last update)
- Only approved jobs are needed

#### Request

`GET /jobs/bms?updateSince=2023-10-26T04:27:33Z`

#### Response

```typescript
const responseSchema = z.object({
  jobs: z.array(jobSchema),
  updateUntil: z.string() // ISO datetime string
})
```

```json
{
  "jobs": [
    { "jobNo": "JR/KLHK/ET08/20/0003", "type": "JR", ... },
    ...
  ],
  "updateUntil": "2023-10-26T04:27:33Z"
}
```

### Update JR / RR to eForm

- Be called when the job is completed / rejected in BMS
- Update following fields on eForm
   - Status
   - Remarks
- Mark as completed in eForm system according to its own logic

#### Request

`POST /jobs/bms`

```typescript
const requestSchema = z.object({
  jobNo: z.string(),
  remarks: z.string(),
  status: EformJobStatus, // enum. options to be provided
})
```

```json
{
  "jobNo": "JR/KLHK/ET08/20/0003", 
  "remarks": "xxx",
  "status": "completed"
}
```

#### Response

```typescript
const responseSchema = jobSchema
```

```json
{
  "jobNo": "JR/KLHK/ET08/20/0003", 
  "type": "JR",
  ...
}
```

### Master Data Retrieval

- Synchronization mechnism TBC
- Retrival from eForm to BMS
   - No updating from BMS to eForm will be included
- Following master data will be needed
   - Company Code, Building, Floor
   - Work nature
- Following master data to be confirmed
   - Item Group
   - Owner Type
   - Cost Centre

#### Request

`GET /master-data/locations`

`GET /master-data/work-nature`

#### Response

```typescript
const responseSchema = z.array(z.object({
  key: z.string(),
  value: z.string(),
}))
```

```typescript
[
  { key: "1", value: "ABC" },
  ...
]
```

```typescript
const locationSchema = z.array(z.object({
  companyCodeKey: z.string(),
  companyCode: z.string(),
  buildingKey: z.string(),
  building: z.string(),
  floorKey: z.string(),
  floor: z.string()
}))
```

```typescript
[
  { ... },
  ...
]
```

