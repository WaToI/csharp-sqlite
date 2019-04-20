# csharp-sqlite

this is pure C# SQLite

### usage
```csharp
var dbfile = $@"{Environment.GetFolderPath(Environment.SpecialFolder.MyDocuments)}\testdb.sqlite.db";
var cnstr = $@"Version=3,uri=file:{dbfile}";

if (File.Exists(dbfile)) {
	File.Delete(dbfile);
}
using (var cn = (IDbConnection)new SqliteConnection(cnstr)) {
	cn.Open();
	using (var cmd = cn.CreateCommand()) {
		cmd.CommandText = "create table testtable (no integer, id text, name text,age integer)";
		cmd.ExecuteNonQuery();
	}

	Func<int> getCount = () => {
		using (var cmd = cn.CreateCommand()) {
			cmd.CommandText = "select count(*) from testtable";
			return int.Parse(cmd.ExecuteScalar().ToString());
		}
	};
	
	var gets = getCount();
	var tr = cn.BeginTransaction();
	using (var cmd = cn.CreateCommand()) {
		var sw = new Stopwatch();
		sw.Start();
		foreach (var i in Enumerable.Range(gets, 10000)) {
			cmd.CommandText = $@"
			insert into testtable (no,id,name,age)
			values({i}, 'test{i}', 'test{i}', {i + 1})";
			cmd.ExecuteNonQuery();
		}
		$"{sw.Elapsed.TotalMilliseconds / 1000} s".Dump();
	}
	tr.Commit();

	Console.WriteLine( getCount());
}
```

forked from 
* https://code.google.com/archive/p/csharp-sqlite/
* https://github.com/koush/csharp-sqlite

